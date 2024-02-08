<script>
  import { Input, Spinner, Label } from "flowbite-svelte";
  import { appStatusInfo, setAppStatusError } from "../store";

  const { id, pages, url } = $appStatusInfo;

  let answer = "";
  let loading = false;

  const numOfImagesToShow = Math.min(pages, 4);
  const images = Array.from({ length: numOfImagesToShow }, (_, i) => {
    return url
      .replace("upload", `upload/w_400,h_540,c_fill,pg_${i + 1}`)
      .replace(".pdf", ".jpg");
  });

  const handlerSubmit = async (event) => {
    event.preventDefault();

    loading = true;
    answer = "";
    const question = event.target.question.value;

    const searchParams = new URLSearchParams();
    searchParams.append("id", id);
    searchParams.append("question", question);

    try {
      const eventSource = new EventSource(
        `/api/ask?${searchParams.toString()}`
      );

      eventSource.onmessage = (event) => {
        loading = false;
        const incomingData = JSON.parse(event.data);

        if (incomingData === "__END__") {
          eventSource.close();
          return;
        }

        answer += incomingData;
      };

      // const res = await fetch(`/api/ask?${searchParams.toString()}`, {
      //   headers: {
      //     "Content-Type": "application/json",
      //   },
      // });

      // if (!res.ok) {
      //   loading = false;
      //   return console.error(`Error asking question`);
      // }

      // const { answer: apiAnswer } = await res.json();
      // answer = apiAnswer;
    } catch (error) {
      setAppStatusError();
      console.error(error);
    } finally {
      loading = false;
    }
  };
</script>

<div class="flex flex-col gap-4">
  <div class="grid grid-cols-4 gap-2">
    {#each images as image}
      <img
        class="rounded-md w-full h-auto aspect-[400/540]"
        src={image}
        alt={`Imagenes de las paginas`}
      />
    {/each}
  </div>

  <form on:submit={handlerSubmit}>
    <Label for="question" color="gray" class="block mb-2">Haz tu pregunta</Label
    >
    <Input
      id="question"
      required
      color="gray"
      placeholder="¿De que trata el PDF?"
    />
  </form>

  {#if loading}
    <div class="flex justify-center items-center flex-col gap-y-2">
      <Spinner color="blue" />
      <p class="opacity-75">Esperando respuesta...</p>
    </div>
  {/if}

  {#if answer}
    <div class="overflow-auto w-full h-[350px] rounded-md bg-slate-100 p-4">
      <p class="font-medium">Respuesta:</p>
      <p>{answer}</p>
    </div>
  {/if}
</div>

<script>
  import Dropzone from "svelte-file-dropzone";
  import {
    setAppStatusLoading,
    appStatus,
    setAppStatusError,
    setAppStatusChatMode,
  } from "../store";

  let files = {
    accepted: [],
    rejected: [],
  };

  async function handleFilesSelect(e) {
    const { acceptedFiles, fileRejections } = e.detail;
    files.accepted = [...files.accepted, ...acceptedFiles];
    files.rejected = [...files.rejected, ...fileRejections];

    if (acceptedFiles.length > 0) {
      const formData = new FormData();
      formData.append("file", acceptedFiles[0]);

      setAppStatusLoading();

      const res = await fetch("/api/upload", {
        method: "POST",
        body: formData,
      });

      if (!res.ok) {
        return setAppStatusError();
      }

      const result = await res.json();

      const { id, pages, url } = result;

      setAppStatusChatMode({ id, pages, url });
    }
  }
</script>

{#if files.accepted.length === 0}
  <Dropzone
    on:drop={handleFilesSelect}
    multiple={false}
    accept="application/pdf"
    disableDefaultStyles
  >
    <div
      class="bg-gray-100 border-2 border-gray-300 border-dashed rounded-xl p-4 flex justify-center"
    >
      <span class="opacity-70 text-center">
        Arrastra y suelta aquí tu PDF
      </span>
    </div>
  </Dropzone>
{/if}

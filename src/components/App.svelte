<script>
  import { Alert } from "flowbite-svelte";
  import { APP_STATUS, appStatus } from "../store";
  import StepUpload from "./StepUpload.svelte";
  import StepLoading from "./StepLoading.svelte";
  import StepChat from "./StepChat.svelte";
</script>

{#if $appStatus === APP_STATUS.INIT}
  <StepUpload />
{:else if $appStatus === APP_STATUS.LOADING}
  <StepLoading />
{:else if $appStatus === APP_STATUS.ERROR}
  <div class="p-8">
    <Alert>
      <span class="font-medium">¡Algo salio mal!</span>
      Ocurrio un error en la aplicación
    </Alert>
  </div>
{:else if $appStatus === APP_STATUS.CHAT_MODE}
  <StepChat />
{:else}
  <div class="p-8">
    <Alert>
      <span class="font-medium">¡Acción no reconocida!</span>
      No se pude reconocer esta acción o estado
    </Alert>
  </div>
{/if}

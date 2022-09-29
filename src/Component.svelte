<script>
  import { getContext, onDestroy } from "svelte"
  import NumberSpinner from "svelte-number-spinner";

  export let field
  export let align
  export let size

  let fieldApi
  let fieldState

  const { styleable } = getContext("sdk")
  const component = getContext("component")
  const formContext = getContext("form")
  const formApi = formContext?.formApi
  $: formField = formApi?.registerField(field, "number", 0, false)

  $: unsubscribe = formField?.subscribe((value) => {
    fieldState = value?.fieldState;
    fieldApi = value?.fieldApi;
  });

  onDestroy(() => {
    fieldApi?.deregister();
    unsubscribe?.();
  });
</script>

<div use:styleable={$component.styles}>
  {#if !formContext}
    <div class="placeholder">Form components need to be wrapped in a form.</div>
  {:else}
    <NumberSpinner 
      class={`spectrum-Textfield spectrum-Textfield-input spectrum-Heading--size${size || "M"}`}
      mainStyle={`text-align: ${align}; height:auto;`}
      value={fieldState?.value}
      on:change={(ev) => fieldApi.setValue(ev.detail)}
    />
  {/if}
</div>
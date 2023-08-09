<script>
  import { getContext, onDestroy } from "svelte"
  import NumberSpinner from "svelte-number-spinner";
  import Label from "./Label.svelte";

  export let field
  export let label
  export let align
  export let size
  export let min
  export let max
  export let step
  export let speed
  export let decimals
  export let circular

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

  const parseStep = () => {
    let parsedStep = parseFloat(step)
    if (parsedStep && !isNaN(parsedStep)) {
      return parsedStep
    }
    return 1
  }
</script>

<div use:styleable={$component.styles}>
  {#if !formContext}
    <div class="placeholder">Form components need to be wrapped in a form.</div>
  {:else}
    <Label>{label}</Label>
    <NumberSpinner 
      min={min || -1000}
      max={max || 1000}
      step={parseStep(step)}
      speed={speed || 1}
      decimals={decimals || 0}
      circular={circular || false}
      class={`spectrum-Textfield spectrum-Textfield-input spectrum-Heading--size${size || "M"}`}
      mainStyle={`text-align: ${align}; height:auto;`}
      value={fieldState?.value}
      on:change={(ev) => fieldApi.setValue(ev.detail)}
    />
  {/if}
</div>
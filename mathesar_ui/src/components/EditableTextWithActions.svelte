<!-- 
  @component

  Enables inline editing with support for "Save" & "Cancel" actions.
 -->
<script lang="ts">
  import {
    CancelOrProceedButtonPair,
    TextInput,
  } from '@mathesar-component-library';
  import { toast } from '@mathesar/stores/toast';
  import { getErrorMessage } from '@mathesar/utils/errors';

  export let initialValue = '';
  export let onSubmit: (value: string) => Promise<void>;
  export let getValidationErrors: (value: string) => string[] = () => [];

  let isEditable = false;
  let value = '';
  let isSubmitting = false;

  $: validationErrors =
    value === initialValue ? [] : getValidationErrors(value);
  $: canSave = validationErrors.length === 0 && value !== initialValue;

  function makeEditable() {
    value = initialValue;
    isEditable = true;
  }

  function handleCancel() {
    value = '';
    isEditable = false;
  }

  async function handleSave() {
    isSubmitting = true;
    try {
      await onSubmit(value);
      value = '';
      isEditable = false;
    } catch (e: unknown) {
      toast.error(getErrorMessage(e));
    } finally {
      isSubmitting = false;
    }
  }
</script>

<div class="editable-text">
  {#if !isEditable}
    <span class="non-editable-value" role="button" on:click={makeEditable}
      >{initialValue}</span
    >
  {:else}
    <div class="input-container">
      <TextInput disabled={isSubmitting} autofocus bind:value />
      {#if validationErrors.length}
        {#each validationErrors as error}
          <span class="error">{error}</span>
        {/each}
      {/if}
      <CancelOrProceedButtonPair
        onProceed={handleSave}
        onCancel={handleCancel}
        isProcessing={isSubmitting}
        canProceed={canSave}
        proceedButton={{ label: 'Save' }}
        size="small"
      />
    </div>
  {/if}
</div>

<style lang="scss">
  .editable-text {
    display: flex;
    flex-direction: column;
    cursor: pointer;
  }

  .input-container {
    display: flex;
    flex-direction: column;
    gap: 0.25rem;
  }

  .error {
    color: var(--color-error);
    font-size: var(--text-size-x-small);
  }

  .non-editable-value {
    padding: 0.43rem 0.57rem;
  }
  .non-editable-value:hover {
    box-shadow: 0 0 0 2px #2087e633;
  }
</style>

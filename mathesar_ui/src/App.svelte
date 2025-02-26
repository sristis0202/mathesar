<script lang="ts">
  import { ToastPresenter, Confirmation } from '@mathesar-component-library';
  import { toast } from '@mathesar/stores/toast';
  import { setNewRecordSelectorControllerInContext } from '@mathesar/systems/record-selector/RecordSelectorController';
  import { confirmationController } from '@mathesar/stores/confirmation';
  import { modal } from './stores/modal';
  import ModalRecordSelector from './systems/record-selector/ModalRecordSelector.svelte';
  import RootRoute from './routes/RootRoute.svelte';

  const recordSelectorModal = modal.spawnModalController();
  const recordSelectorController = setNewRecordSelectorControllerInContext({
    onOpen: () => recordSelectorModal.open(),
    onClose: () => recordSelectorModal.close(),
  });
</script>

<ToastPresenter entries={toast.entries} />
<Confirmation controller={confirmationController} />
<ModalRecordSelector
  {recordSelectorController}
  modalController={recordSelectorModal}
/>

<RootRoute />

<!--
  Supporting aliases in scss within the preprocessor is a bit of work.
  I looked around to try to get it done but it didn't seem important to
  spend time figuring this out.

  The component-library style import would only ever be from App.svelte
  and when the library is moved to a separate package, we wouldn't have to
  worry about aliases.
-->
<style global lang="scss">
  @import 'component-library/styles.scss';

  :root {
    /** BASE COLORS **/
    --color-white: #ffffff;
    --color-blue-light: #e6f0ff;
    --color-blue-medium: #3b82f6;
    --color-blue-dark: #1d4ed8;
    --color-orange-dark: #7c2d12;
    --color-green-medium: #10b981;
    --color-gray-lighter: #fafafa;
    --color-gray-light: #f4f4f5;
    --color-gray-medium: #d4d4d8;
    --color-gray-dark: #a1a1aa;
    --color-gray-darker: #27272a;
    --color-contrast: var(--color-blue-medium);
    --color-contrast-light: var(--color-blue-light);
    --color-link: var(--color-blue-dark);
    --color-text: #171717;
    --color-text-muted: #6b7280;
    --text-size-x-small: 0.79rem;
    --text-size-xx-small: 0.69rem;
    --text-size-small: 0.889rem;
    --text-size-base: 1rem;
    --text-size-large: 1.125rem;
    --text-size-x-large: 1.266rem;
    --display-size-large: 1.953rem;
  }

  body {
    /**
   * This sets the `mix-blend-mode` property for cell backgrounds.
   *
   * Why use color blending instead of opacity? Because I thought it would give
   * us an easier time keeping all our UI colors in sync. With blending, we
   * supply the exact same color value as we'd use for another places in the UI
   * where we expect the color to be opaque.
   *
   * If/when we implement dark mode, we'll need to toggle this property to
   * something like `screen` or `lighten` so that as more backgrounds are
   * applied, the resulting blended background gets lighter instead of darker.
   */
    --cell-bg-mix-blend-mode: multiply;
    /**
   * This establishes a base background color for the cell when no additional
   * background colors are applied. We need this in case there is a background
   * color applied underneath the cell, e.g. on the table or page.
   */
    --cell-bg-color-base: white;
    --cell-bg-color-error: #fef1f1;
    --cell-bg-color-header: #f9f9f9;
    --cell-bg-color-processing: #fefef1;
    --cell-bg-color-disabled: #f3f3f3;
    --cell-bg-color-row-hover: #f6f7f7;
    --cell-bg-color-row-selected: #e4f2ff;

    --color-fk: #dfd0b3;
    --color-error: #f47171;
    --cell-text-color-processing: #888;

    --cell-border-horizontal: 1px solid #e7e7e7;
    --cell-border-vertical: 1px solid #efefef;

    --page-padding: 1em;

    color: var(--color-text);
  }

  h1 {
    margin: 0 0 1rem 0;
    font-size: 1.6rem;
  }

  /**
   * Used to turn elements like `<button>` and `<a>` into plain elements that
   * don't have any browser styling but still have functionality.
   */
  .passthrough {
    background: inherit;
    border-radius: inherit;
    border: inherit;
    color: inherit;
    cursor: inherit;
    font-family: inherit;
    font-size: inherit;
    font-weight: inherit;
    text-align: inherit;
    margin: 0;
    padding: 0;
  }
</style>

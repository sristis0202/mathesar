<script lang="ts">
  import {
    getCellKey,
    getTabularDataStoreFromContext,
  } from '@mathesar/stores/table-data';
  import type { Row } from '@mathesar/stores/table-data/types';
  import {
    getRowKey,
    ID_ROW_CONTROL_COLUMN,
  } from '@mathesar/stores/table-data';
  import { SheetRow, SheetCell } from '@mathesar/components/sheet';
  import { isRowSelected } from '@mathesar/stores/table-data/selection';
  import RowControl from './RowControl.svelte';
  import RowCell from './RowCell.svelte';
  import GroupHeader from './GroupHeader.svelte';
  import NewRecordMessage from './NewRecordMessage.svelte';

  export let row: Row;
  export let style: { [key: string]: string | number };

  const tabularData = getTabularDataStoreFromContext();

  $: ({
    recordsData,
    columnsDataStore,
    meta,
    display,
    processedColumns,
    selection,
  } = $tabularData);
  $: ({
    // selectedRows,
    rowStatus,
    rowCreationStatus,
    cellModificationStatus,
    cellClientSideErrors,
  } = meta);
  $: ({ grouping } = recordsData);

  $: ({ primaryKeyColumnId } = $columnsDataStore);
  $: rowKey = getRowKey(row, primaryKeyColumnId);
  $: creationStatus = $rowCreationStatus.get(rowKey)?.state;
  $: status = $rowStatus.get(rowKey);
  $: wholeRowState = status?.wholeRowState;
  $: ({ selectedCells } = selection);
  $: isSelected = isRowSelected($selectedCells, row);
  $: hasWholeRowErrors = wholeRowState === 'failure';
  /** Including whole row errors and individual cell errors */
  $: hasAnyErrors = !!status?.errorsFromWholeRowAndCells?.length;

  function checkAndCreateEmptyRow() {
    if (row.isAddPlaceholder) {
      void recordsData.addEmptyRecord();
    }
  }

  const handleRowClick = () => {
    if (
      row.record &&
      !row.isAddPlaceholder &&
      typeof row.rowIndex === 'number'
    ) {
      selection.toggleRowSelection(row);
    }
  };
</script>

<SheetRow {style} let:htmlAttributes let:styleString>
  <div
    class="row"
    class:selected={isSelected}
    class:processing={wholeRowState === 'processing'}
    class:failed={hasWholeRowErrors}
    class:created={creationStatus === 'success'}
    class:add-placeholder={row.isAddPlaceholder}
    class:new={row.isNew}
    class:is-group-header={row.isGroupHeader}
    class:is-add-placeholder={row.isAddPlaceholder}
    {...htmlAttributes}
    style={styleString}
    data-row-identifier={row.identifier}
    on:mousedown={checkAndCreateEmptyRow}
  >
    <SheetCell
      columnIdentifierKey={ID_ROW_CONTROL_COLUMN}
      isStatic
      let:htmlAttributes
      let:style
    >
      <div
        class="row-control"
        {...htmlAttributes}
        {style}
        on:click={handleRowClick}
      >
        {#if row.record}
          <RowControl
            {primaryKeyColumnId}
            {row}
            {meta}
            {recordsData}
            {isSelected}
            hasErrors={hasAnyErrors}
          />
        {/if}
      </div>
    </SheetCell>

    {#if row.isNewHelpText}
      <NewRecordMessage columnCount={$processedColumns.size} />
    {:else if row.isGroupHeader && $grouping && row.group}
      <GroupHeader
        {row}
        grouping={$grouping}
        group={row.group}
        processedColumnsMap={$processedColumns}
      />
    {:else if row.record}
      {#each [...$processedColumns] as [columnId, processedColumn] (columnId)}
        <RowCell
          {display}
          {selection}
          {row}
          rowHasErrors={hasWholeRowErrors}
          key={getCellKey(rowKey, columnId)}
          modificationStatusMap={cellModificationStatus}
          clientSideErrorMap={cellClientSideErrors}
          bind:value={row.record[columnId]}
          dataForRecordSummaryInFkCell={row.dataForRecordSummariesInRow?.[
            columnId
          ]}
          {processedColumn}
          {recordsData}
        />
      {/each}
    {/if}
  </div>
</SheetRow>

<style lang="scss">
  .row {
    &.processing {
      pointer-events: none;
    }

    &:not(:hover) :global(.cell-bg-row-hover) {
      display: none;
    }

    .row-control {
      font-size: var(--text-size-x-small);
      padding: 0 1.5rem;
      color: var(--color-text-muted);
      display: inline-flex;
      align-items: center;
      height: 100%;
    }

    &.is-add-placeholder {
      cursor: pointer;

      :global([data-sheet-element='cell']:not(.is-active)
          .cell-fabric
          .cell-wrapper
          > *) {
        visibility: hidden;
      }
    }
  }
</style>

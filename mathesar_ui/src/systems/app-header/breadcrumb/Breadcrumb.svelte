<script lang="ts">
  import Logo from '@mathesar/components/Logo.svelte';
  import { getDatabasePageUrl } from '@mathesar/routes/urls';
  import BreadcrumbItem from './BreadcrumbItem.svelte';
  import { getBreadcrumbItemsFromContext } from './breadcrumbUtils';

  const items = getBreadcrumbItemsFromContext();

  $: isMinimal =
    $items.length === 0 || $items.every((i) => i.type === 'database');
  $: minimalHref = (() => {
    const fallback = '/';
    if (!isMinimal) {
      return fallback;
    }
    const database = $items.find((i) => i.type === 'database')?.database;
    if (!database) {
      return fallback;
    }
    return getDatabasePageUrl(database.name);
  })();
</script>

<div class="breadcrumb">
  {#if isMinimal}
    <a href={minimalHref} class="home-link">
      <Logo />
      <span class="mathesar">Mathesar</span>
    </a>
  {:else}
    {#each $items as item}
      <BreadcrumbItem {item} />
    {/each}
  {/if}
</div>

<style>
  .breadcrumb {
    display: flex;
    align-items: center;
    --spacing: 0.5rem;
    margin: calc(-1 * var(--spacing));
  }
  .breadcrumb > :global(*) {
    margin: var(--spacing);
  }
  .home-link {
    display: flex;
    align-items: center;
    text-decoration: none;
  }
  .mathesar {
    font-weight: 500;
    display: block;
    color: var(--color-gray-darker);
    font-size: var(--text-size-x-large);
    margin: 0 var(--spacing);
  }
</style>

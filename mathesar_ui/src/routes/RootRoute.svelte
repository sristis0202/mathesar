<script lang="ts">
  import { Route } from 'tinro';

  import ErrorPage from '@mathesar/pages/ErrorPage.svelte';
  import { databases } from '@mathesar/stores/databases';
  import { setBreadcrumbItemsInContext } from '@mathesar/systems/app-header/breadcrumb/breadcrumbUtils';
  import DatabaseRoute from './DatabaseRoute.svelte';
  import { getDatabasePageUrl } from './urls';

  setBreadcrumbItemsInContext([]);

  $: firstDatabase = $databases.data?.[0];
</script>

{#if firstDatabase}
  <Route path="/" redirect={getDatabasePageUrl(firstDatabase.name)} />
{:else}
  <Route path="/">
    <ErrorPage>No databases found</ErrorPage>
  </Route>
{/if}

<Route path="/:databaseName/*" let:meta firstmatch>
  <DatabaseRoute databaseName={meta.params.databaseName} />
</Route>

<script>
  import { supressWarnings } from './supress-warnings'

  export let type = undefined
  export let tokens = undefined
  export let header = undefined
  export let rows = undefined
  export let ordered = false
  export let renderers
  export let events
  export let context
  supressWarnings();
</script>

{#if !type}
  {#each tokens as token}
    <svelte:self {...token} {renderers} {events} {context}/>
  {/each}
{:else}
  {#if renderers[type]}
    {#if type === 'table'}
      <svelte:component this={renderers.table} on:message={events.table} {context}>
        <svelte:component this={renderers.tablehead} on:message={events.tablehead} {context}>
          <svelte:component this={renderers.tablerow} on:message={events.tablerow} {context}>
            {#each header as headerItem, i}
              <svelte:component
                this={renderers.tablecell}
                header={true}
                align={$$restProps.align[i] || 'center'}
                on:message={events.tablecell}
                {context}
                >
                <svelte:self tokens={headerItem.tokens} {renderers} {events} {context}/>
              </svelte:component>
            {/each}
          </svelte:component>
        </svelte:component>
        <svelte:component this={renderers.tablebody} on:message={events.tablebody} {context}>
          {#each rows as row}
            <svelte:component this={renderers.tablerow}  on:message={events.tablerow} {context}>
              {#each row as cells, i}
                <svelte:component
                  this={renderers.tablecell}
                  header={false}
                  align={$$restProps.align[i] || 'center'}
                  on:message={events.tablecell}
                  {context}
                  >
                  <svelte:self tokens={cells.tokens} {renderers} {events} {context}/>
                </svelte:component>
              {/each}
            </svelte:component>
          {/each}
        </svelte:component>
      </svelte:component>
    {:else if type === 'list'}
      {#if ordered}
        <svelte:component this={renderers.list} {ordered} {...$$restProps}  on:message={events.list} {context}>
          {#each $$restProps.items as item}
            <svelte:component this={renderers.orderedlistitem || renderers.listitem} {...item}  on:message={events.orderedlistitem || events.listitem} {context}>
              <svelte:self tokens={item.tokens} {renderers} {events} {context}/>
            </svelte:component>
          {/each}
        </svelte:component>
      {:else}
        <svelte:component this={renderers.list} {ordered} {...$$restProps}  on:message={events.list} {context}>
          {#each $$restProps.items as item}
            <svelte:component this={renderers.unorderedlistitem || renderers.listitem} {...item}  on:message={events.unorderedlistitem || events.listitem} {context}>
              <svelte:self tokens={item.tokens} {renderers} {events} {context}/>
            </svelte:component>
          {/each}
        </svelte:component>
      {/if}
    {:else}
      <svelte:component this={renderers[type]} {...$$restProps}  on:message={events[type]} {context}>
        {#if tokens}
          <svelte:self {tokens} {renderers} {events} {context}/>
        {:else}
          {$$restProps.raw}
        {/if}
      </svelte:component>
    {/if}
  {/if}
{/if}

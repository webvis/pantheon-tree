<script>
    import { results } from 'anymapper'
    
    import List, {Item, Text, Graphic} from '@smui/list'
</script>

<style>
    a, a:hover, a:visited {
        color: inherit;
        text-decoration: none;
    }
    :global(.results .mdc-list-item__graphic) {
        margin-right: 16px;
    }
</style>


<List singleSelection class="results">
    {#each $results as r}
        <a href="#{r.id}">    
            <Item>
                <Graphic class="material-icons">person</Graphic>
                <Text>{r.id}</Text>
            </Item>
        </a>
    {/each}
</List>
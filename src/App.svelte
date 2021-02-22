<script>
	import { Content } from '@smui/card'

	import { selection, selected_id, View, Layer, InfoBox, InfoBoxHeader, OmniBox, ResultsBox, ObservableNotebook, make_selectable } from 'anymapper'
	import notebook from '@nitaku/tangled-tree-visualization-ii'

	const pantheon = {
		'Zeus': {
			description: `Zeus is the sky and thunder god in ancient Greek religion, who rules as king of the gods of Mount Olympus. His name is cognate with the first element of his Roman equivalent Jupiter. His mythology and powers are similar, though not identical, to those of Indo-European deities such as Jupiter, Perkūnas, Perun, Indra, Dyaus and Thor.`
		}
	}

	function updateSelection(_) {
		if($selected_id in pantheon)
			$selection = pantheon[$selected_id]
		else
			$selection = null
	}

	selected_id.subscribe(updateSelection)
</script>

<style>
	/* FIXME? global deafults? */
	:global(html), :global(body) {
		margin: 0;
		padding: 0;
		overflow: hidden;
	}
	.wrapper {
		height: 100%;
		width: 100%;
		overflow-y: auto;
		overflow-x: hidden;
		position: absolute;
	}

	:global(a), :global(a:hover), :global(a:visited) {
		text-decoration: none;
		color: var(--primary-bg-color);
	}
	:global(a:hover) {
		text-decoration: underline;
	}

	/* application-specific */
	
	/* define global CSS */
	:global(.infobox) {
		width: 350px;
	}
	:global(.omnibox) {
		width: 350px;
	}

	:global(.selectable) {
		cursor: pointer;
	}

	:global(:root) {
		--infobox-header-height: 86px;
		--omnibox-margin: 10px;
	}
</style>

<div class="wrapper">

<View viewBox="0 0 850 850">
	<Layer name="default">
		<ObservableNotebook
		  notebook={notebook}
		  variable_name="chart"
		  on:ready={(e) => make_selectable(e.detail.node, '.selectable', (n) => n.getAttribute('data-id'))}
		/>
	</Layer>
</View>

<OmniBox>
	<ResultsBox>
		Hi
	</ResultsBox>
</OmniBox>

<InfoBox>
	<InfoBoxHeader title="{$selected_id}" subtitle=""/>
	<Content>{$selection.description}</Content>
</InfoBox>

</div>

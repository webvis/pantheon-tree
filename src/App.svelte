<script>
	import { Content } from '@smui/card'

	import { selection, selected_id, View, Layer, InfoBox, InfoBoxHeader, OmniBox, ResultsBox, ObservableNotebook, Depiction, make_selectable } from 'anymapper'
	import notebook from '@nitaku/tangled-tree-visualization-ii'

	const pantheon = {
		'Zeus': {
			depiction: "https://upload.wikimedia.org/wikipedia/commons/thumb/0/01/Bust_of_Zeus.jpg/440px-Bust_of_Zeus.jpg",
			description: `Zeus is the sky and thunder god in ancient Greek religion, who rules as king of the gods of Mount Olympus. His name is cognate with the first element of his Roman equivalent Jupiter. His mythology and powers are similar, though not identical, to those of Indo-European deities such as Jupiter, Perkūnas, Perun, Indra, Dyaus and Thor.`
		},
		'Hera': {
			depiction: "https://upload.wikimedia.org/wikipedia/commons/thumb/7/76/Hera_Campana_Louvre_Ma2283.jpg/440px-Hera_Campana_Louvre_Ma2283.jpg",
			description: `Hera (/ˈhɛrə, ˈhɪərə/; Greek: Ἥρᾱ, Hērā; Ἥρη, Hērē in Ionic and Homeric Greek) is the goddess of women, marriage, family and childbirth in ancient Greek religion and mythology, one of the Twelve Olympians and the sister and wife of Zeus. She is the daughter of the Titans Cronus and Rhea. Hera rules over Mount Olympus as queen of the gods. A matronly figure, Hera served as both the patroness and protectress of married women, presiding over weddings and blessing marital unions. One of Hera's defining characteristics is her jealous and vengeful nature against Zeus' numerous lovers and illegitimate offspring, as well as the mortals who cross her.`
		},
		'Alcmene': {
			description: `In Greek mythology, Alcmene (/ælkˈmiːniː/) or Alcmena (/ælkˈmiːnə/; Ancient Greek: Ἀλκμήνη or Doric Greek: Ἀλκμάνα, Latin: Alcumena means "strong in wrath") was the wife of Amphitryon by whom she bore two children, Iphicles and Laonome. She is best known as the mother of Heracles, whose father was the god Zeus. Alcmene was also referred to as Electryone (Ἠλεκτρυώνην), a patronymic name as a daughter of Electryon.`
		},
		'Hercules': {
			depiction: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a9/Hercules_Farnese_3637104088_9c95d7fe3c_b.jpg/800px-Hercules_Farnese_3637104088_9c95d7fe3c_b.jpg",
			description: `Heracles (/ˈhɛrəkliːz/ HERR-ə-kleez; Greek: Ἡρακλῆς, Hēraklês, Glory/Pride of Hēra, "Hera"), born Alcaeus (Ἀλκαῖος, Alkaios) (/ælˈsiːəs/) or Alcides (Ἀλκείδης, Alkeidēs) (/ælˈsaɪdiːz/), was a divine hero in Greek mythology, the son of Zeus and Alcmene, and the foster son of Amphitryon. He was a great-grandson and half-brother (as they are both sired by the god Zeus) of Perseus. He was the greatest of the Greek heroes, a paragon of masculinity, the ancestor of royal clans who claimed to be Heracleidae (Ἡρακλεῖδαι), and a champion of the Olympian order against chthonic monsters. In Rome and the modern West, he is known as Hercules, with whom the later Roman emperors, in particular Commodus and Maximian, often identified themselves. The Romans adopted the Greek version of his life and works essentially unchanged, but added anecdotal detail of their own, some of it linking the hero with the geography of the Central Mediterranean. Details of his cult were adapted to Rome as well.`
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
	:global(.view) {
		background: rgb(241, 239, 229);
	}

	:global(.selectable) {
		cursor: pointer;
	}

	:global(:root) {
		--infobox-header-height: 86px;
		--omnibox-margin: 10px;
		--primary-bg-color: rgb(102, 102, 102);
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
	<InfoBoxHeader title="{$selected_id}"/>
	{#if $selection.depiction}
		<Depiction src={$selection.depiction} positionY="top" aspectRatio="square"/>
	{/if}
	<Content>{$selection.description}</Content>
</InfoBox>

</div>

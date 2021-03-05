<script>
	import * as d3 from 'd3'
	import lunr from 'lunr'
	import { Content } from '@smui/card'
	import marked from 'marked'

	import { selection, selected_id, View, Layer, InfoBox, InfoBoxHeader, OmniBox, ResultsBox, ObservableNotebook, Depiction, Placemark, make_selectable, centroid, results } from 'anymapper'
	import notebook from '@nitaku/tangled-tree-visualization-ii'
	
	import ResultsList from './ResultsList.svelte'

	const pantheon = {
		'Zeus': {
			depiction: "https://upload.wikimedia.org/wikipedia/commons/thumb/0/01/Bust_of_Zeus.jpg/440px-Bust_of_Zeus.jpg",
			description: `
**Zeus** is the sky and thunder god in ancient Greek religion, who rules as king of the gods of Mount Olympus. His name is cognate with the first element of his Roman equivalent Jupiter. His mythology and powers are similar, though not identical, to those of Indo-European deities such as Jupiter, Perkūnas, Perun, Indra, Dyaus and Thor.

Zeus is the child of [Cronus](#Cronus) and [Rhea](#Rhea), the youngest of his siblings to be born, though sometimes reckoned the eldest as the others required disgorging from Cronus's stomach. In most traditions, he is married to [Hera](#Hera), by whom he is usually said to have fathered [Ares](#Ares), [Hebe](#Hebe), and [Hephaestus](#Hephaestus). At the oracle of Dodona, his consort was said to be [Dione](#Dione), by whom the *Iliad* states that he fathered [Aphrodite](#Aphrodite). Zeus was also infamous for his erotic escapades. These resulted in many divine and heroic offspring, including [Athena](#Athena), [Apollo](#Apollo), [Artemis](#Artemis), [Hermes](#Hermes), [Persephone](#Persephone), Dionysus, Perseus, [Heracles](#Hercules), [Helen of Troy](#Helen), Minos, and the Muses.`
		},
		'Hera': {
			depiction: "https://upload.wikimedia.org/wikipedia/commons/thumb/7/76/Hera_Campana_Louvre_Ma2283.jpg/440px-Hera_Campana_Louvre_Ma2283.jpg",
			description: `
**Hera** (/ˈhɛrə, ˈhɪərə/; Greek: Ἥρᾱ, Hērā; Ἥρη, Hērē in Ionic and Homeric Greek) is the goddess of women, marriage, family and childbirth in ancient Greek religion and mythology, one of the Twelve Olympians and the sister and wife of [Zeus](#Zeus). She is the daughter of the Titans [Cronus](#Cronus) and [Rhea](#Rhea). Hera rules over Mount Olympus as queen of the gods. A matronly figure, Hera served as both the patroness and protectress of married women, presiding over weddings and blessing marital unions. One of Hera's defining characteristics is her jealous and vengeful nature against Zeus' numerous lovers and illegitimate offspring, as well as the mortals who cross her.`
		},
		'Alcmene': {
			description: `
In Greek mythology, **Alcmene** (/ælkˈmiːniː/) or Alcmena (/ælkˈmiːnə/; Ancient Greek: Ἀλκμήνη or Doric Greek: Ἀλκμάνα, Latin: Alcumena means "strong in wrath") was the wife of Amphitryon by whom she bore two children, Iphicles and Laonome. She is best known as the mother of [Heracles](#Heracles), whose father was the god [Zeus](#Zeus). Alcmene was also referred to as Electryone (Ἠλεκτρυώνην), a patronymic name as a daughter of Electryon.`
		},
		'Hercules': {
			depiction: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a9/Hercules_Farnese_3637104088_9c95d7fe3c_b.jpg/800px-Hercules_Farnese_3637104088_9c95d7fe3c_b.jpg",
			description: `
**Heracles** (/ˈhɛrəkliːz/ HERR-ə-kleez; Greek: Ἡρακλῆς, Hēraklês, Glory/Pride of Hēra, "Hera"), born Alcaeus (Ἀλκαῖος, Alkaios) (/ælˈsiːəs/) or Alcides (Ἀλκείδης, Alkeidēs) (/ælˈsaɪdiːz/), was a divine hero in Greek mythology, the son of [Zeus](#Zeus) and [Alcmene](#Alcmene), and the foster son of Amphitryon. He was a great-grandson and half-brother (as they are both sired by the god Zeus) of Perseus. He was the greatest of the Greek heroes, a paragon of masculinity, the ancestor of royal clans who claimed to be Heracleidae (Ἡρακλεῖδαι), and a champion of the Olympian order against chthonic monsters.`
		},
		'Aphrodite': {
			depiction: 'https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/NAMA_Aphrodite_Syracuse.jpg/440px-NAMA_Aphrodite_Syracuse.jpg',
			description: `
**Aphrodite** is an ancient Greek goddess associated with love, beauty, pleasure, passion and procreation. She was syncretized with the Roman goddess Venus. Aphrodite's major symbols include myrtles, roses, doves, sparrows, and swans.

The cult of Aphrodite was largely derived from that of the Phoenician goddess Astarte, a cognate of the East Semitic goddess Ishtar, whose cult was based on the Sumerian cult of Inanna. Aphrodite's main cult centers were Cythera, Cyprus, Corinth, and Athens. Her main festival was the Aphrodisia, which was celebrated annually in midsummer. In Laconia, Aphrodite was worshipped as a warrior goddess. She was also the patron goddess of prostitutes, an association which led early scholars to propose the concept of "sacred prostitution" in Greco-Roman culture, an idea which is now generally seen as erroneous.`
		},
		'Cronus': {
			depiction: 'https://upload.wikimedia.org/wikipedia/commons/thumb/2/27/Saturnus_fig274.png/440px-Saturnus_fig274.png',
			description: `
In Greek mythology, **Cronus**, Cronos, or Kronos (/ˈkroʊnəs/ or /ˈkroʊnɒs/, US: /-oʊs/, from Greek: Κρόνος, Krónoς) was the leader and youngest of the first generation of Titans, the divine descendants of Uranus, the sky, and Gaia, the earth. He overthrew his father and ruled during the mythological Golden Age, until he was overthrown by his own son [Zeus](#Zeus) and imprisoned in Tartarus. According to Plato, however, the deities Phorcys, Cronus, and Rhea were the eldest children of Oceanus and Tethys.`
		}
	}
	Object.keys(pantheon).forEach(id => pantheon[id].id = id)

	function updateSelection(_) {
		if($selected_id in pantheon)
			$selection = pantheon[$selected_id]
		else
			$selection = null
	}

	selected_id.subscribe(updateSelection)

	// search
	let index = lunr(function () {
		let this_index = this
		this_index.pipeline.remove(lunr.stemmer)
		this_index.searchPipeline.remove(lunr.stemmer)

		this_index.ref('id')
		this_index.field('id')
		this_index.field('description')

		Object.keys(pantheon).forEach(id => this_index.add(pantheon[id]))
	})

	function handleSearch(e) {
		$results = search(e.detail.query)
	}

	function search(query) {
		if(query == '') {
			return []
		}

		let actual_query = query.trim().split(/\s+/).map(term => '+'+term+'*').join(' ')
		
		let results = index.search(`${actual_query}`).map(d => pantheon[d.ref])
		
		return results
	}

	function handleNotebookReady(e) {
		let id_accessor = (n) => n.getAttribute('data-id')

		make_selectable(e.detail.node, '.selectable', id_accessor)
		d3.select(e.detail.node).selectAll('.selectable.node').each(function() {
			if(id_accessor(this) in pantheon)
				pantheon[id_accessor(this)].position = centroid(this)
		})
	}
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
	}
</style>

<div class="wrapper">

<View viewBox="0 0 850 850">
	<Layer name="default">
		<ObservableNotebook
		  notebook={notebook}
		  variable_name="chart"
		  on:ready={ handleNotebookReady }
		/>
	</Layer>
	<Placemark icon="person"/>
</View>

<OmniBox on:search={ handleSearch }>
	<ResultsBox>
		<ResultsList/>
	</ResultsBox>
</OmniBox>

<InfoBox>
	<InfoBoxHeader title="{$selected_id}"/>
	{#if $selection.depiction}
		<Depiction src={$selection.depiction} positionY="top" aspectRatio="square"/>
	{/if}
	<Content>{@html marked($selection.description)}</Content>
</InfoBox>

</div>

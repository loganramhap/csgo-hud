<template>
	<div v-if="series.length > 1" :class="['series', { '--active': active }]">
		<div v-for="match in series" :class="['match', { '--currently-playing': match.currentlyPlaying }]">
			<RoundGraph v-if="match.currentlyPlaying" :directionalSides="directionalSides" />

			<div v-else class="result">
				<template v-if="match.scoreLeft || match.scoreRight">
					<img v-if="teams[0].flag" :src="`https://flagcdn.com/${teams[0].flag.toLowerCase()}.svg`">
					{{ match.scoreLeft }} : {{ match.scoreRight }}
					<img v-if="teams[1].flag" :src="`https://flagcdn.com/${teams[1].flag.toLowerCase()}.svg`">
				</template>

				<template v-else>&nbsp;</template>
			</div>

			<div class="inner">
				<div :class="`map --${match.map}`">
					<img ref="image" :src="matchMap(match.map)" height=160vh width=100% style="object-fit: cover;">
				</div>

				<div
					v-if="match.scoreLeft > match.scoreRight && teams[0].flag"
					:class="`winner-flag-background ${flagStyle(teams[0].flag)}`"
					:style="{ backgroundImage: `url(https://flagcdn.com/${teams[0].flag.toLowerCase()}.svg)` }"
				></div>

				<div
					v-else-if="match.scoreRight > match.scoreLeft && teams[1].flag"
					:class="`winner-flag-background ${flagStyle(teams[1].flag)}`"
					:style="{ backgroundImage: `url(https://flagcdn.com/${teams[1].flag.toLowerCase()}.svg)` }"
				></div>

				<div class="text">
					<div class="map-name">{{ formatMapName(match.map) }}</div>
					<div class="pick-heading">{{ match.picked ? 'Pick' : 'Decider' }}</div>
					<div class="pick-team">{{ match.picked ? teams[match.picked].name || '&nbsp;' : '&nbsp;' }}</div>
				</div>
			</div>
		</div>
	</div>
	<div v-else-if="series.length == 1" :class="['series', { '--active': active }]">
		<RoundGraph directionalSides="directionalSides"/>
	</div>
</template>

<script>
import { mapGetters } from 'vuex'
import RoundGraph from './RoundGraph'
import flagStyle from '../flag-style'
import formatMapName from '../map-names'
import { match } from 'assert'

export default {
	components: {
		RoundGraph,
	},

	props: [
		'directionalSides',
	],

	computed: {
		...mapGetters([
			'map',
			'series',
			'timers',
		]),

		mapName() {
			return this.map.name.replace(/^.*\//, '')
		},

		active() {
			return this.map.phase === 'intermission'
				|| ['paused', 'timeout_ct', 'timeout_t', 'freezetime'].includes(this.timers.phase)
		},

		teams() {
			return [
				this.map[`team_${this.directionalSides[0]}`],
				this.map[`team_${this.directionalSides[1]}`],
			]
		},



		
	},

	methods: {
		flagStyle,
		formatMapName,
		matchMap(map) {
			console.log(map)
			return require(`../../img/map/${map.toLowerCase()}.png`).default
		},
	},
}
</script>

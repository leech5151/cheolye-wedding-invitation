<script lang="ts">
	import calendarDeco from '$lib/assets/calendar-deco.svg';
	import { localeStore } from '../i18n.svelte';
	import { _ } from 'svelte-i18n';

	// July 2026 - 4th is Saturday (wedding day)
	// July 1 = Wednesday → rows: [28,29,30,1,2,3,4], [5..11], [12..18], [19..25], [26..31]
	const HIGHLIGHT_DATE = 4;

	const weeks: (number | null)[][] = [
		[28, 29, 30, 1, 2, 3, 4], // Sun 6/28 - Sat 7/4
		[5, 6, 7, 8, 9, 10, 11],
		[12, 13, 14, 15, 16, 17, 18],
		[19, 20, 21, 22, 23, 24, 25],
		[26, 27, 28, 29, 30, 31, null]
	];
</script>

<section class="calendar">
	<h2 class="title {localeStore.locale}">{$_('calendar.title')}</h2>
	<div class="calendar-content">
		{#each weeks as week, weekIndex}
			<div class="week-row">
				{#each week as date, dayIndex}
					<div class="date-cell">
						{#if date !== null}
							{#if date === HIGHLIGHT_DATE}
								<div class="highlighted-wrapper">
									<span class="date-num highlighted"><span class="num-inner">{date}</span></span>
									<span class="calendar-time-small {localeStore.locale}">{$_('calendar.time')}</span>
								</div>
							{:else}
								<span class="date-num" class:sunday={dayIndex === 0} class:prev-month={weekIndex === 0 && date >= 28}>{date}</span>
							{/if}
						{:else}
							<span class="date-num empty"></span>
						{/if}
					</div>
				{/each}
			</div>
		{/each}
	</div>
	<img class="calendar-deco" src={calendarDeco} alt="calendar deco" />
</section>

<style lang="scss">
	$calendar-text: #5b5651;
	$sunday-red: #d95858;

	.calendar {
		position: relative;
		background-color: $white;
		padding: 2em 3.5em 4em 3.5em;

		.title {
			text-align: center;
			margin-bottom: 1em;
			&.kr {
				@extend .title-font-kr;
			}
			&.en {
				@extend .title-font-en;
			}
		}

		.calendar-content {
			display: flex;
			flex-direction: column;
			gap: 0.5em;
		}

		.week-row {
			display: flex;
			justify-content: space-between;
			align-items: center;
			gap: 0.5em;
			height: 3em;
			flex-shrink: 0;
		}

		.date-cell {
			display: flex;
			align-items: center;
			justify-content: center;
			flex: 1;
			min-width: 0;
			height: 100%;
		}

		.highlighted-wrapper {
			position: relative;
			display: flex;
			align-items: center;
			justify-content: center;
			height: 100%;
		}

		.date-num {
			display: inline-flex;
			align-items: center;
			justify-content: center;
			width: 2.2em;
			height: 2.2em;
			line-height: 1;
			padding: 0;
			margin: 0;
			font-family: 'Cormorant Garamond', serif;
			font-size: 1.25rem;
			font-weight: 500;
			color: $calendar-text;

			&.sunday {
				color: $sunday-red;
			}

			&.prev-month {
				color: #c4bfba;
			}

			&.empty {
				visibility: hidden;
			}

			&.highlighted {
				width: 1.5em;
				height: 1.5em;
				border-radius: 50%;
				background-color: $primary-color;
				color: white;
				font-weight: 600;
				font-size: 1.35rem;
				transform: translateY(5px);
				.num-inner {
					display: block;
					padding-bottom: 0.3em;
				}
			}
		}

		.calendar-time-small {
			position: absolute;
			top: 100%;
			left: 50%;
			transform: translateX(-50%);
			margin-top: 0.1em;
			font-size: 0.65rem;
			color: $primary-color;
			white-space: nowrap;
			pointer-events: none;
			&.kr {
				font-weight: 500;
			}
			&.en {
				font-weight: 600;
			}
		}

		img.calendar-deco {
			position: absolute;
			bottom: 0.5em;
			right: 2.5em;
		}
	}
</style>

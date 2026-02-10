<script lang="ts">
	import locationTopWave from '$lib/assets/location-top-wave.svg';
	import locationDeco from '$lib/assets/location-deco.svg';
	import locationMap from '$lib/assets/location-map.png';
	import { _ } from 'svelte-i18n';
	import { localeStore } from '../i18n.svelte';
	import { Clipboard, Github } from '@lucide/svelte';

	const addressKr = '서울 용산구 이태원로 29 로얄파크';
	const addressEn = 'Royal Park, 29 Itaewon-ro, Yongsan-gu, Seoul';

	function copyAddress() {
		const text = localeStore.locale === 'kr' ? addressKr : addressEn;
		navigator.clipboard
			.writeText(text)
			.then(() => alert($_('location.address_copied')))
			.catch(() => null);
	}
</script>

<img src={locationTopWave} class="location-top-wave" alt="" />
<section class="location">
	<h2 class="title {localeStore.locale}">{$_('location.title')}</h2>
	<p class="venue {localeStore.locale}">{$_('location.venue')}</p>
	<button class="copy-address {localeStore.locale}" onclick={copyAddress}>
		<span class="clipboard-icon">
			<Clipboard size="1em" />
		</span>
		<span class="address">{$_('location.address')}</span></button
	>
	<p class="floor-notice {localeStore.locale}">{$_('location.floor_notice')}</p>
	<a class="location-map-link" href="https://naver.me/IFgdRRqM" target="_blank" rel="noopener noreferrer">
		<span class="map-hint {localeStore.locale}">{$_('location.map_hint')}</span>
		<img class="location-map" src={locationMap} alt="로얄파크 컨벤션 위치" />
	</a>
	<p class="signature en">made with ♡ by Cheol-hee & Ye-jin</p>
	<a class="github-icon" href="https://github.com/anthopark/our-wedding-invitation" target="_blank"
		><Github size="1.1em" strokeWidth={1} /></a
	>
	<img class="location-deco" src={locationDeco} alt="" />
</section>

<style lang="scss">
	img.location-top-wave {
		max-width: $content-max-width;
		margin: auto;
	}

	section.location {
		position: relative;
		display: flex;
		flex-direction: column;
		align-items: center;
		position: relative;
		background-color: $bg-color-1;
		padding: 1em 2em 1em 2em;
	}

	h2.title {
		color: $primary-color;
		text-align: center;
		margin-bottom: 0.8em;

		&.kr {
			@extend .title-font-kr;
			letter-spacing: 1px;
			font-size: 1.1rem;
		}

		&.en {
			@extend .title-font-en;
			letter-spacing: 1px;
			font-size: 1.3rem;
		}
	}

	p.venue {
		font-size: 0.95rem;
	}

	button.copy-address {
		display: flex;
		align-items: center;
		margin-top: 0.4em;

		.clipboard-icon {
			height: 1em;
			display: inline-block;
			margin-right: 0.2em;
			color: $font-color-default;
		}

		.address {
			display: inline-block;
			font-size: 0.9rem;
			text-decoration: underline;
		}
	}

	p.floor-notice {
		margin-top: 0.5em;
		font-size: 0.8rem;
		color: $font-color-light;

		&.kr,
		&.en {
			text-align: center;
		}
	}

	a.location-map-link {
		position: relative;
		display: block;
		margin-top: 2em;
		margin-bottom: 7em;
		cursor: pointer;
	}

	span.map-hint {
		position: absolute;
		top: 0.5em;
		left: 50%;
		transform: translateX(-50%);
		font-size: 0.75rem;
		color: $font-color-light;
		z-index: 1;
	}

	img.location-map {
		width: 100%;
		max-width: $content-max-width;
		height: auto;
		border-radius: 8px;
		box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
		display: block;
	}

	p.signature {
		font-size: 1rem;
	}
	.github-icon {
		margin-top: 0.2em;
		color: $font-color-default;
		cursor: pointer;
	}

	img.location-deco {
		position: absolute;
		bottom: 2.5em;
		right: 1.5em;
	}
</style>

<script lang="ts">
	import locationTopWave from '$lib/assets/location-top-wave.svg';
	import locationDeco from '$lib/assets/location-deco.svg';
	import { _ } from 'svelte-i18n';
	import { base } from '$app/paths';
	import { localeStore } from '../i18n.svelte';
	import { Clipboard, Github } from '@lucide/svelte';
	import { onMount } from 'svelte';
	import Rsvp from './rsvp.svelte';

	const addressKr = '서울 용산구 이태원로 29 로얄파크';
	const addressEn = 'Royal Park, 29 Itaewon-ro, Yongsan-gu, Seoul';

	function copyAddress() {
		const text = localeStore.locale === 'kr' ? addressKr : addressEn;
		navigator.clipboard
			.writeText(text)
			.then(() => alert($_('location.address_copied')))
			.catch(() => null);
	}

	const kakaoAppKey = '46dfff729a8998eb0cfa0e6f84b735ba';
	const inviteUrl = 'https://leech5151.github.io/cheolye-wedding-invitation/';
	const kakaoMapUrl = 'https://kko.to/ptrfQ12DQ9';
	const naverMapUrl = 'https://map.naver.com/v5/search/로얄파크컨벤션';
	const tmapUrl = 'https://tmap.co.kr';
	const placeKeyword = '로얄파크컨벤션';
	// 로얄파크컨벤션 fallback (삼각지역 12번출구 인근)
	const fallbackLat = 37.5348;
	const fallbackLng = 126.9712;

	onMount(() => {
		const kakao = (window as Window & { Kakao?: any }).Kakao;
		if (kakao && !kakao.isInitialized()) {
			kakao.init(kakaoAppKey);
		}

		function createMap(km: any, lat: number, lng: number) {
			const container = document.getElementById('kakao-map');
			if (!container) return;
			const coords = new km.LatLng(lat, lng);
			const options = { center: coords, level: 5, draggable: false, scrollwheel: false };
			const map = new km.Map(container, options);
			const marker = new km.Marker({ position: coords });
			marker.setMap(map);
		}

		function initMap() {
			const w = window as Window & { kakao?: { maps: any } };
			if (!w.kakao?.maps?.load) {
				setTimeout(initMap, 150);
				return;
			}
			const km = w.kakao.maps;
			km.load(() => {
				const places = new km.services.Places();
				places.keywordSearch(placeKeyword, (result: { x: string; y: string; place_name: string }[], status: string) => {
					const ok = km.services.Status?.OK ?? 'OK';
					if (status === ok && result?.[0]) {
						const place = result[0];
						createMap(km, Number(place.y), Number(place.x));
					} else {
						createMap(km, fallbackLat, fallbackLng);
					}
				});
			});
		}
		initMap();
	});

	function shareKakao() {
		const kakao = (window as Window & { Kakao?: any }).Kakao;
		if (!kakao) return;
		kakao.Link.sendDefault({
			objectType: 'feed',
			content: {
				title: '이철희 & 이예진의 결혼식',
				description: '2026년 7월 4일 로얄파크컨벤션',
				imageUrl: 'https://leech5151.github.io/cheolye-wedding-invitation/cover.png',
				link: {
					mobileWebUrl: inviteUrl,
					webUrl: inviteUrl
				}
			},
			buttons: [
				{
					title: '청첩장보기',
					link: {
						mobileWebUrl: inviteUrl,
						webUrl: inviteUrl
					}
				},
				{
					title: '위치',
					link: {
						mobileWebUrl: kakaoMapUrl,
						webUrl: kakaoMapUrl
					}
				}
			]
		});
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
	<div class="parking-info {localeStore.locale}">
		<p class="parking-title">{$_('location.parking.title')}</p>
		<ul>
			<li>{$_('location.parking.item1')}</li>
			<li>{$_('location.parking.item3')}</li>
		</ul>
	</div>
	<a class="map-wrapper map-link" href={kakaoMapUrl} target="_blank" rel="noopener noreferrer">
		<div id="kakao-map" class="kakao-map"></div>
	</a>
	<div class="map-buttons">
		<a class="map-btn" href={tmapUrl} target="_blank" rel="noopener noreferrer">
			<img src={`${base}/tmap-logo.png`} alt="티맵" class="map-btn-icon" />
			<span class="map-btn-text">티맵</span>
		</a>
		<a class="map-btn" href={kakaoMapUrl} target="_blank" rel="noopener noreferrer">
			<img src={`${base}/kakaonavi-icon.svg`} alt="카카오네비" class="map-btn-icon" />
			<span class="map-btn-text">카카오네비</span>
		</a>
		<a class="map-btn" href={naverMapUrl} target="_blank" rel="noopener noreferrer">
			<img src={`${base}/navermap-icon.svg`} alt="네이버지도" class="map-btn-icon" />
			<span class="map-btn-text">네이버지도</span>
		</a>
	</div>
	<button class="kakao-share" onclick={shareKakao}>카카오톡으로 공유</button>
	<Rsvp />
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

	.parking-info {
		margin-top: 0.8em;
		font-size: 0.8rem;
		color: $font-color-light;

		&.kr,
		&.en {
			text-align: center;
		}

		.parking-title {
			margin-bottom: 0.3em;
			font-weight: 600;
		}

		ul {
			margin: 0;
			padding-left: 0;
			list-style: none;
		}

		li {
			margin-top: 0.2em;
		}
	}

	.map-wrapper {
		position: relative;
		margin-top: 2em;
		width: 100%;
		max-width: $content-max-width;
		display: block;
		text-decoration: none;
		cursor: pointer;
	}

	.map-wrapper.map-link .kakao-map {
		pointer-events: none;
	}

	.kakao-map {
		width: 100%;
		height: 280px;
		border-radius: 12px;
		border: 2px solid $primary-color-light-2;
		box-shadow: 0 4px 16px rgba(185, 148, 147, 0.2);
		overflow: hidden;
	}

	.map-buttons {
		display: flex;
		gap: 1em;
		margin-top: 1em;
		margin-bottom: 0em;
		width: 100%;
		max-width: $content-max-width;

		@media (max-width: 410px) {
			gap: 0.25em;
		}
	}

	.map-btn {
		flex: 1;
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: center;
		gap: 0.35em;
		padding: 0.5em 0.35em;
		background: $white;
		border: 1px solid $primary-color-light-2;
		border-radius: 8px;
		font-family: 'Noto Serif KR', serif;
		font-size: 0.9rem;
		font-weight: 500;
		color: $primary-color;
		text-decoration: none;

		@media (max-width: 410px) {
			gap: 0;
			padding: 0.5em;
			font-size: 0.75rem;
		}
	}

	.map-btn-text {
		@media (max-width: 410px) {
			display: none;
		}
	}

	.map-btn-icon {
		width: 24px;
		height: 24px;
		object-fit: contain;
		flex-shrink: 0;
		margin-right: 0.5em;

		@media (max-width: 410px) {
			width: 18px;
			height: 18px;
		}
	}

	button.kakao-share {
		margin-top: 0.8em;
		margin-bottom: 2em;
		padding: 0.6em 1.2em;
		border-radius: 4px;
		background-color: #fee500;
		font-family: 'Noto Serif KR', serif;
		font-size: 0.9rem;
		font-weight: 500;
		color: #3c1e1e;
	}

	p.signature {
		font-size: 1rem;
		margin-top: 2em;
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

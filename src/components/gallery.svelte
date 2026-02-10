<script lang="ts">
	const galleryModules = import.meta.glob<{ default: string }>('$lib/assets/gallery/*.png', {
		eager: true
	});

	const photos = Object.keys(galleryModules)
		.filter((path) => !path.endsWith('014.png'))
		.sort()
		.map((path) => ({ src: galleryModules[path].default }));

	import PhotoSwipeLightBox from 'photoswipe/lightbox';
	import PhotoSwipe from 'photoswipe';
	import 'photoswipe/style.css';
	import { onMount } from 'svelte';
	import { localeStore } from '../i18n.svelte';
	import { _ } from 'svelte-i18n';

	onMount(() => {
		const gallery = document.querySelector('#gallery');
		if (!gallery) return;

		const slides = gallery.querySelectorAll('a.slide');
		let loaded = 0;
		let inited = false;

		const tryInit = () => {
			loaded++;
			if (loaded < slides.length || inited) return;
			inited = true;

			const lightbox = new PhotoSwipeLightBox({
				gallery: '#gallery',
				children: 'a',
				showHideAnimationType: 'fade',
				pswpModule: PhotoSwipe
			});
			lightbox.init();
		};

		slides.forEach((anchor) => {
			const img = anchor.querySelector('img');
			if (!img) {
				tryInit();
				return;
			}

			const setSize = () => {
				anchor.setAttribute('data-pswp-width', String(img.naturalWidth));
				anchor.setAttribute('data-pswp-height', String(img.naturalHeight));
				tryInit();
			};

			if (img.complete) setSize();
			else img.addEventListener('load', setSize);
		});

		if (slides.length === 0) tryInit();
	});
</script>

<section class="gallery">
	<div class="header">
		<h2 class="title {localeStore.locale}">{$_('gallery.title')}</h2>
		<p class="sub-title {localeStore.locale}">{$_('gallery.sub_title')}</p>
	</div>
	<div id="gallery">
		{#each photos as photo}
			<a href={photo.src} class="slide" target="_blank">
				<img class="thumbnail" src={photo.src} alt="" />
			</a>
		{/each}
	</div>
</section>

<style lang="scss">
	section.gallery {
		padding: 4.5em 2em 2em 2em;
		background-color: $white;
	}

	.header {
		margin-bottom: 2em;
	}

	h2.title {
		text-align: center;
		&.en {
			@extend .title-font-en;
			letter-spacing: 1px;
		}

		&.kr {
			@extend .title-font-kr;
			letter-spacing: 1px;
		}
	}

	p.sub-title {
		text-align: center;
		&.kr {
			margin-top: 0.9em;
			font-size: 0.9rem;
		}

		&.en {
			margin-top: 0.5em;
			font-size: 1.2rem;
		}
	}

	#gallery {
		display: grid;
		gap: 1em;
		grid-template-columns: repeat(3, 1fr);
		grid-auto-rows: 10em;
	}

	a.slide {
		display: flex;
		align-items: center;
		justify-content: center;
		min-height: 0;
		background: #f8f6f4;
		border-radius: 4px;
	}

	img.thumbnail {
		border-radius: 4px;
		width: 100%;
		height: 100%;
		object-fit: contain;
	}
</style>

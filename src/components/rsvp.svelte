<script lang="ts">
	import { _ } from 'svelte-i18n';
	import { localeStore } from '../i18n.svelte';
	import { Clipboard } from '@lucide/svelte';

	function copyText(text: string) {
		navigator.clipboard
			.writeText(text)
			.then(() => alert($_('gift.account_copied')))
			.catch(() => null);
	}
</script>

<section class="rsvp gift">
	<h2 class="title {localeStore.locale}">{$_('gift.title')}</h2>
	<button class="copy-item {localeStore.locale}" onclick={() => copyText($_('gift.bank_name'))}>
		<span class="clipboard-icon"><Clipboard size="1em" /></span>
		<span class="address">{$_('gift.bank_name')}</span>
	</button>
	<button class="copy-item {localeStore.locale}" onclick={() => copyText($_('gift.account_number'))}>
		<span class="clipboard-icon"><Clipboard size="1em" /></span>
		<span class="address">{$_('gift.account_number')}</span>
	</button>
	<button class="copy-item {localeStore.locale}" onclick={() => copyText($_('gift.holder_name'))}>
		<span class="clipboard-icon"><Clipboard size="1em" /></span>
		<span class="address">{$_('gift.holder_name')}</span>
	</button>
</section>

<style lang="scss">
	section.rsvp.gift {
		display: flex;
		flex-direction: column;
		align-items: center;
		margin-top: 0.5em;
		margin-bottom: 0.5em;
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

	button.copy-item {
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
</style>

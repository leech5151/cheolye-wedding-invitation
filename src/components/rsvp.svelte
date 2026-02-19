<script lang="ts">
	import { _ } from 'svelte-i18n';
	import { localeStore } from '../i18n.svelte';
	import { Clipboard, X } from '@lucide/svelte';

	function copyText(text: string) {
		if (!text) return;
		navigator.clipboard
			.writeText(text)
			.then(() => alert($_('gift.account_copied')))
			.catch(() => null);
	}

	type AccountRow = { labelKey: string; valueKey: string; holderKey: string };
	const groomRows: AccountRow[] = [
		{ labelKey: 'gift.groom_account_label', valueKey: 'gift.groom_account', holderKey: 'gift.groom_account_holder' },
		{ labelKey: 'gift.groom_host_mother_label', valueKey: 'gift.groom_host_mother', holderKey: 'gift.groom_host_mother_holder' },
		{ labelKey: 'gift.groom_host_father_label', valueKey: 'gift.groom_host_father', holderKey: 'gift.groom_host_father_holder' }
	];
	const brideRows: AccountRow[] = [
		{ labelKey: 'gift.bride_account_label', valueKey: 'gift.bride_account', holderKey: 'gift.bride_account_holder' },
		{ labelKey: 'gift.bride_host_mother_label', valueKey: 'gift.bride_host_mother', holderKey: 'gift.bride_host_mother_holder' },
		{ labelKey: 'gift.bride_host_father_label', valueKey: 'gift.bride_host_father', holderKey: 'gift.bride_host_father_holder' }
	];

	function formatAccountDisplay(row: AccountRow): string {
		const value = $_(row.valueKey);
		const holder = $_(row.holderKey);
		if (!value) return '—';
		if (holder) return `${value} (${holder})`;
		return value;
	}

	let popup: 'groom' | 'bride' | null = null;

	function openPopup(side: 'groom' | 'bride') {
		popup = side;
	}
	function closePopup() {
		popup = null;
	}
</script>

<section class="rsvp gift">
	<h2 class="title {localeStore.locale}">{$_('gift.title')}</h2>
	<div class="gift-buttons">
		<button class="gift-btn {localeStore.locale}" onclick={() => openPopup('groom')}>
			{$_('gift.groom_btn')}
		</button>
		<button class="gift-btn {localeStore.locale}" onclick={() => openPopup('bride')}>
			{$_('gift.bride_btn')}
		</button>
	</div>
</section>

{#if popup}
	<div class="popup-backdrop" role="dialog" aria-modal="true" aria-label="계좌 안내" onclick={closePopup}>
		<div class="popup-box" onclick={(e) => e.stopPropagation()}>
			<button class="popup-close" onclick={closePopup} aria-label="{$_('gift.close')}">
				<X size="1.25em" />
			</button>
			<div class="popup-list">
				{#if popup === 'groom'}
					{#each groomRows as row}
						<div class="popup-row">
							<span class="popup-label">{$_(row.labelKey)}</span>
							<span class="popup-value">{formatAccountDisplay(row)}</span>
							{#if $_(row.valueKey)}
								<button
									class="popup-copy"
									onclick={() => copyText($_(row.valueKey))}
									title="{$_('gift.account_copied')}"
								>
									<Clipboard size="1em" />
								</button>
							{/if}
						</div>
					{/each}
				{:else}
					{#each brideRows as row}
						<div class="popup-row">
							<span class="popup-label">{$_(row.labelKey)}</span>
							<span class="popup-value">{formatAccountDisplay(row)}</span>
							{#if $_(row.valueKey)}
								<button
									class="popup-copy"
									onclick={() => copyText($_(row.valueKey))}
									title="{$_('gift.account_copied')}"
								>
									<Clipboard size="1em" />
								</button>
							{/if}
						</div>
					{/each}
				{/if}
			</div>
			<button class="popup-close-btn {localeStore.locale}" onclick={closePopup}>
				{$_('gift.close')}
			</button>
		</div>
	</div>
{/if}

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

	.gift-buttons {
		display: flex;
		gap: 0.8em;
		flex-wrap: wrap;
		justify-content: center;
	}

	.gift-btn {
		padding: 0.6em 1.2em;
		border-radius: 8px;
		border: 1px solid $primary-color-light-2;
		background: $white;
		color: $primary-color;
		font-family: 'Noto Serif KR', serif;
		font-size: 0.95rem;
		font-weight: 500;
		cursor: pointer;

		&:hover {
			background: $primary-color-light-2;
		}
	}

	.popup-backdrop {
		position: fixed;
		inset: 0;
		background: rgba(0, 0, 0, 0.5);
		display: flex;
		align-items: center;
		justify-content: center;
		z-index: 1000;
		padding: 1em;
	}

	.popup-box {
		background: $white;
		border-radius: 12px;
		padding: 1.5em;
		max-width: 92vw;
		width: min(420px, 92vw);
		position: relative;
		box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
	}

	.popup-close {
		position: absolute;
		top: 0.75em;
		right: 0.75em;
		background: none;
		border: none;
		color: $font-color-default;
		cursor: pointer;
		padding: 0.25em;
	}

	.popup-list {
		display: flex;
		flex-direction: column;
		gap: 0.75em;
		margin-top: 0.5em;
		margin-bottom: 1em;
	}

	.popup-row {
		display: flex;
		align-items: center;
		gap: 0.5em;
		flex-wrap: wrap;
		padding: 0.5em 0;
		border-bottom: 1px solid $primary-color-light-2;

		&:last-child {
			border-bottom: none;
		}
	}

	.popup-label {
		font-weight: 600;
		color: $primary-color;
		min-width: 7em;
		font-size: 0.9rem;
	}

	.popup-value {
		flex: 1;
		min-width: 0;
		font-size: 0.85rem;
		color: $font-color-default;
		word-break: break-all;
	}

	.popup-copy {
		background: none;
		border: none;
		color: $primary-color;
		cursor: pointer;
		padding: 0.25em;
		flex-shrink: 0;
	}

	.popup-close-btn {
		display: block;
		width: 100%;
		padding: 0.6em;
		border-radius: 8px;
		border: 1px solid $primary-color-light-2;
		background: $white;
		color: $primary-color;
		font-family: 'Noto Serif KR', serif;
		font-size: 0.9rem;
		cursor: pointer;

		&:hover {
			background: $primary-color-light-2;
		}
	}
</style>

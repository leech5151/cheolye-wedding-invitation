<script lang="ts">
	import { _ } from 'svelte-i18n';
	import { localeStore } from '../i18n.svelte';
	import { LoaderCircle } from '@lucide/svelte';
	import { env } from '$env/dynamic/public';
	import RsvpSelect from './rsvp-select.svelte';
	import rsvpDeco from '$lib/assets/rsvp-deco.svg';

	let rsvp = $state<'yes' | 'no' | null>(null);
	let submitting = $state(false);
	let message = $state<{ type: 'success' | 'error'; key: string } | null>(null);

	const formspreeEndpoint = env.PUBLIC_FORMSPREE_ENDPOINT || '';

	function clearMessage() {
		message = null;
	}

	async function handleSubmit(e: SubmitEvent) {
		e.preventDefault();
		const form = e.target as HTMLFormElement;
		const name = (form.elements.namedItem('fullname') as HTMLInputElement)?.value?.trim();

		if (!name) {
			message = { type: 'error', key: 'missing_name_error' };
			return;
		}
		if (!rsvp) {
			message = { type: 'error', key: 'missing_rsvp_error' };
			return;
		}
		if (!formspreeEndpoint) {
			message = { type: 'error', key: 'email_error' };
			return;
		}

		submitting = true;
		message = null;

		try {
			const res = await fetch(formspreeEndpoint, {
				method: 'POST',
				headers: { 'Content-Type': 'application/json' },
				body: JSON.stringify({
					fullname: name,
					rsvp,
					_subject: `[Wedding RSVP] ${name}`
				})
			});

			if (res.ok) {
				message = { type: 'success', key: 'email_success' };
				rsvp = null;
				form.reset();
			} else {
				message = { type: 'error', key: 'email_error' };
			}
		} catch {
			message = { type: 'error', key: 'email_error' };
		} finally {
			submitting = false;
		}
	}
</script>

<section class="rsvp">
	<div class="header">
		<img class="header-deco" src={rsvpDeco} alt="rsvp header deco" />
		<h2 class="title {localeStore.locale}">{$_('rsvp.title')}</h2>
		<p class="sub-title {localeStore.locale}">
			{$_('rsvp.reply_by')}
		</p>
	</div>

	<form class="rsvp-form" onsubmit={handleSubmit}>
		<input
			class="fullname {localeStore.locale}"
			name="fullname"
			placeholder={$_('rsvp.fullname_placeholder')}
			onfocus={clearMessage}
		/>
		<div class="select-container">
			<RsvpSelect bind:rsvp clearForm={clearMessage} />
		</div>
		<button class="send {localeStore.locale}" type="submit" disabled={submitting}>
			{#if submitting}
				<div class="spinning">
					<LoaderCircle />
				</div>
			{:else}
				{$_('rsvp.send')}
			{/if}
		</button>
	</form>
	<div class="submit-message">
		{#if message}
			<p class="{message.type} {localeStore.locale}">
				{$_(`rsvp.${message.key}`)}
			</p>
		{/if}
	</div>

	<p class="floor-notice {localeStore.locale}">{$_('rsvp.floor_notice')}</p>
</section>

<style lang="scss">
	section.rsvp {
		padding: 4.5em 3.5em;
	}

	.header {
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	img.header-deco {
		width: 4.5em;
		margin-bottom: 0.8em;
	}

	h2.title {
		color: $primary-color;

		&.kr {
			@extend .title-font-kr;
			letter-spacing: 1px;
		}

		&.en {
			@extend .title-font-en;
			letter-spacing: 3px;
		}
	}

	p.sub-title {
		&.kr {
			margin-top: 0.9em;
			font-size: 0.9rem;
		}

		&.en {
			margin-top: 0.5em;
			font-size: 1.2rem;
		}
	}

	form.rsvp-form {
		margin-top: 3em;
	}

	input.fullname {
		padding: 0.4em 0.8em;
		width: 100%;
		border: 1px solid $white-2;
		border-radius: 4px;
		letter-spacing: 0.02em;

		&:active,
		&:focus {
			@extend .input-focused;
		}
		&::placeholder {
			color: $font-color-light;
		}
		&.kr::placeholder {
			font-size: 0.9rem;
		}
	}

	.select-container {
		margin-top: 1em;
	}

	button.send {
		display: flex;
		justify-content: center;
		align-items: center;
		margin-top: 3em;
		height: 2.5em;
		width: 100%;
		background-color: $primary-color;
		border-radius: 4px;
		color: $white;
		letter-spacing: 0.05em;

		&[disabled] {
			background-color: $primary-color-light;
			cursor: not-allowed;
		}

		&:active {
			background-color: $primary-color-dark;
		}

		&.kr {
			font-weight: 600;
		}

		&.en {
			font-weight: 700;
		}
	}

	@keyframes spin {
		0% {
			transform: rotate(0deg);
		}
		100% {
			transform: rotate(359deg);
		}
	}

	.spinning {
		display: flex;
		justify-content: center;
		align-items: center;
		animation: spin 1s linear infinite;
	}

	.submit-message {
		margin-top: 0.5em;
		height: 1.5em;

		.kr {
			font-size: 0.9rem;
		}

		.en {
			font-size: 1.1rem;
		}

		p.success {
			color: $green-1;
		}

		p.error {
			color: $red-1;
		}
	}

	.floor-notice {
		margin-top: 2em;
		text-align: center;
		color: $primary-color;
		&.kr {
			font-size: 0.95rem;
		}
		&.en {
			font-size: 1rem;
		}
	}
</style>

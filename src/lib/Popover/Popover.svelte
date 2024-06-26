<script lang="ts">
	import {
		createPopper,
		type Instance,
		type Modifier,
		type OptionsGeneric,
	} from "@popperjs/core";

	import Portal from "./Portal.svelte";
	import { onDestroy } from "svelte";
	import { useClickOutside } from "./useClickOutside";

	/**
	 * Specifies the element to anchor the menu to.
	 */
	export let anchorEl: HTMLElement;

	/**
	 * Specifies whether the menu is open or not.
	 */
	export let open: boolean;

	/**
	 * Specifies whether the class name to use for the popover element.
	 */
	export let className: string = "popover layer";

	/**
	 * Specifies a function to run when the popover closes.
	 */
	export let onClose: () => void = () => {};

	/**
	 * Specifices where to place the menu in relation to the anchor element.
	 */
	export let placement:
		| "auto"
		| "auto-start"
		| "auto-end"
		| "top"
		| "bottom"
		| "right"
		| "left"
		| "top-start"
		| "top-end"
		| "bottom-start"
		| "bottom-end"
		| "right-start"
		| "right-end"
		| "left-start"
		| "left-end" = "bottom-start";

	let popperEl: HTMLElement;

	let popper: Instance | null = null;

	const params: Partial<OptionsGeneric<Partial<Modifier<"offset", any>>>> = {
		placement,
		modifiers: [
			{
				name: "offset",
				options: {
					offset: [0, 4],
				},
			},
		],
	};

	$: {
		if (anchorEl && popperEl) {
			popper = createPopper(anchorEl, popperEl, params);
		}
	}

	$: if (!open) {
		onClose();
	}

	onDestroy(() => {
		if (popper) {
			popper.destroy();
		}
	});
</script>

{#if open}
	<Portal target={anchorEl.ownerDocument.body}>
		<div
			class={className}
			bind:this={popperEl}
			use:useClickOutside={{
				open,
				anchorEl,
				onClickOutside: () => (open = false),
			}}
		>
			<slot />
		</div>
	</Portal>
{/if}

<style>
	.layer {
		z-index: var(--layer-popover);
	}
</style>

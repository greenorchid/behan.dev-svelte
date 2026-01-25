<script lang="ts">
	/* eslint-disable svelte/no-navigation-without-resolve */
	import { onMount } from 'svelte';
	import 'highlight.js/styles/github-dark.css';
	import { goto } from '$app/navigation';
	import { base } from '$app/paths';
	import { page } from '$app/state';
	import Tooltip from '$lib/components/Tooltip.svelte';
	import BlueskyComments from '$lib/components/bluesky/BlueskyComments.svelte';
	import { initializeAgent } from '$lib/components/bluesky/client';
	import { CONFIG } from '$lib/config';

	let { data } = $props();
	const post = $derived(data.post);

	onMount(() => {
		initializeAgent();
	});

	function getAiBadgeBorder(level: string) {
		switch (level) {
			case 'none':
				return 'text-green-700 dark:text-green-400 border-green-500';
			case 'partial':
				return 'text-yellow-700 dark:text-yellow-400 border-yellow-500';
			case 'considerable':
				return 'text-red-700 dark:text-red-400 border-red-500';
			default:
				return 'text-gray-700 dark:text-gray-400 border-gray-500';
		}
	}

	function getAiTooltip(level: string) {
		switch (level) {
			case 'none':
				return 'No AI contributions were used in creating this article.';
			case 'partial':
				return 'Partial AI contributions: Image/diagram generation, external research assistance, or minor enhancements.';
			case 'considerable':
				return 'Considerable AI contributions: Article summarization, primary research, content generation, or significant AI-driven insights.';
			default:
				return 'AI contribution level not specified.';
		}
	}
</script>

{#if post}
	<article id="main-content" class="container mx-auto max-w-4xl px-4 py-8">
		<!-- eslint-disable-next-line svelte/no-navigation-without-resolve -->
		<button
			onclick={() => goto(`${base}/blog`)}
			class="mb-6 inline-flex items-center text-sm text-gray-600 transition-colors hover:text-green-600 dark:text-gray-400 dark:hover:text-green-400"
		>
			<svg
				xmlns="http://www.w3.org/2000/svg"
				class="mr-2 h-4 w-4"
				fill="none"
				viewBox="0 0 24 24"
				stroke="currentColor"
			>
				<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
			</svg>
			Back to Blog
		</button>

		<div
			class="rounded-lg border border-gray-200 bg-gray-100 p-6 md:p-8 dark:border-gray-700 dark:bg-gray-800"
		>
			<header class="mb-6">
				<h1 class="mb-4 text-4xl font-bold text-gray-900 md:text-5xl dark:text-gray-100">
					{post.title}
				</h1>
				<p class="mb-4 text-sm text-gray-500 dark:text-gray-400">{post.date}</p>
				{#if post.tags.length > 0}
					<div class="flex flex-wrap gap-2">
						{#each post.tags as tag (tag)}
							<span
								class="rounded-full border border-green-200 bg-green-100 px-3 py-1 text-sm text-green-700 dark:border-green-800 dark:bg-green-900/30 dark:text-green-400"
							>
								{tag}
							</span>
						{/each}
					</div>
				{/if}
				<div class="mt-6 flex flex-wrap items-center justify-between gap-4">
					<Tooltip
						content={getAiTooltip(post.aiContributions)}
						trigger="mouseenter focus"
						placement="top"
					>
						<span
							class="inline-flex items-center rounded-lg border-2 bg-white px-4 py-2 text-sm font-medium dark:bg-gray-800 {getAiBadgeBorder(
								post.aiContributions
							)}"
						>
							🤖 AI: {post.aiContributions}
						</span>
					</Tooltip>

					<a
						href="https://bsky.app/intent/compose?text={encodeURIComponent(
							`Read "${post.title}" by @${CONFIG.blueskyHandle}\n\n${page.url.href}`
						)}"
						target="_blank"
						rel="noopener noreferrer"
						class="inline-flex items-center gap-2 rounded-lg bg-[#0085ff] px-4 py-2 text-sm font-semibold text-white transition-colors hover:bg-[#0070d6]"
					>
						<svg class="h-4 w-4" fill="currentColor" viewBox="0 0 24 24">
							<path
								d="M23.169 11.237c-.368-2.124-2.583-4.22-5.46-4.22-3.155 0-5.708 2.096-5.708 4.675 0 2.578 2.553 4.674 5.708 4.674h.01c2.81 0 4.152-1.342 4.152-1.342s1.42 2.062 4.908 2.062c3.486 0 5.215-2.062 5.093-4.54-.08-1.597-1.12-2.731-2.903-3.303 3.65-.262 6.275-2.072 6.275-4.507 0-2.348-2.457-4.267-6.078-4.639.012.13.018.261.018.395 0 3.08-2.61 5.576-5.83 5.576-3.218 0-5.83-2.496-5.83-5.576 0-.134.006-.265.018-.395-3.62.372-6.077 2.291-6.077 4.639 0 2.435 2.624 4.245 6.274 4.507-1.783.572-2.822 1.706-2.903 3.303-.122 2.478 1.607 4.54 5.093 4.54 3.488 0 4.909-2.062 4.909-2.062s1.34 1.342 4.15 1.342h.01c3.156 0 5.71-2.096 5.71-4.674 0-2.579-2.554-4.675-5.71-4.675-2.876 0-5.092 2.096-5.46 4.22z"
							/>
						</svg>
						Share on Bluesky
					</a>

					{#if post.blueskyUri}
						<a
							href="https://bsky.app/profile/{post.blueskyUri.split('/')[2]}/post/{post.blueskyUri
								.split('/')
								.pop()}"
							target="_blank"
							rel="noopener noreferrer"
							class="text-sm text-blue-600 hover:underline dark:text-blue-400"
						>
							View on Bluesky
						</a>
					{/if}
				</div>
			</header>

			<div
				class="prose prose-lg max-w-none dark:prose-invert prose-headings:text-gray-900 dark:prose-headings:text-gray-100 prose-p:text-gray-700 dark:prose-p:text-gray-300 prose-a:text-green-600 prose-a:no-underline hover:prose-a:underline dark:prose-a:text-green-400 prose-strong:text-gray-900 dark:prose-strong:text-gray-100 prose-code:rounded prose-code:bg-gray-100 prose-code:px-1.5 prose-code:py-0.5 prose-code:text-green-600 dark:prose-code:bg-gray-800 dark:prose-code:text-green-400 prose-pre:border prose-pre:border-gray-700 prose-pre:bg-gray-900 dark:prose-pre:bg-gray-950"
			>
				<!-- eslint-disable-next-line svelte/no-at-html-tags -->
				{@html post.html}
			</div>

			{#if post.blueskyUri}
				<BlueskyComments postUri={post.blueskyUri} />
			{/if}
		</div>
	</article>
{/if}

<style>
	:global(.prose pre code) {
		color: inherit;
		background: transparent !important;
		padding: 0;
	}
	:global(.prose pre) {
		background: rgb(17 24 39) !important; /* gray-900 */
	}
	:global(.dark .prose pre) {
		background: rgb(3 7 18) !important; /* gray-950 */
	}
	:global(.prose code::before),
	:global(.prose code::after) {
		content: '';
	}
	:global(.prose pre code.hljs) {
		background: transparent !important;
	}
</style>

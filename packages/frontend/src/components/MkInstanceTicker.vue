<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<div :class="$style.root" :style="bg">
	<img v-if="faviconUrl" :class="$style.icon" :src="faviconUrl"/>
	<div :class="$style.name">{{ instance.name }}</div>
</div>
</template>

<script lang="ts" setup>
import { computed } from 'vue';
import { instanceName } from '@@/js/config.js';
import { instance as Instance } from '@/instance.js';
import { getProxiedImageUrlNullable } from '@/scripts/media-proxy.js';

const props = defineProps<{
	instance?: {
		faviconUrl?: string | null
		name?: string | null
		themeColor?: string | null
	}
}>();

// if no instance data is given, this is for the local instance
const instance = props.instance ?? {
	name: instanceName,
	themeColor: (document.querySelector('meta[name="theme-color-orig"]') as HTMLMetaElement).content,
};

const faviconUrl = computed(() => props.instance ? getProxiedImageUrlNullable(props.instance.faviconUrl, 'preview') : getProxiedImageUrlNullable(Instance.iconUrl, 'preview') ?? '/favicon.ico');

const themeColor = instance.themeColor ?? '#777777';

const bg = {
	background: `linear-gradient(90deg, ${themeColor}, ${themeColor}30)`,
};
</script>

<style lang="scss" module>
$height: 2ex;

.root {
	display: flex;
	align-items: center;
	height: $height;
	border-radius: 24px;
	overflow: clip;
	color: #000;
	padding: 2px 4px;
	text-shadow: /* .866 ≈ sin(60deg) */
		1px 0 1px #fff,
		.866px .5px 1px #fff,
		.5px .866px 1px #fff,
		0 1px 1px #fff,
		-.5px .866px 1px #fff,
		-.866px .5px 1px #fff,
		-1px 0 1px #fff,
		-.866px -.5px 1px #fff,
		-.5px -.866px 1px #fff,
		0 -1px 1px #fff,
		.5px -.866px 1px #fff,
		.866px -.5px 1px #fff;
	mask-image: linear-gradient(90deg,
		rgb(0,0,0),
		rgb(0,0,0) calc(100% - 16px),
		rgba(0,0,0,0) 100%
	);
}

.icon {
	height: $height;
	flex-shrink: 0;
	border-radius: 4px;
	height: 0.9em;
}

.name {
	margin-left: 4px;
	line-height: 1;
	font-size: 0.8em;
	font-weight: bold;
	white-space: nowrap;
	overflow: visible;
}
</style>

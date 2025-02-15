<template>
	<div class="BuilderSwitcher">
		<div :class="{ active: activeId == 'ui' }" @click="selectOption('ui')">
			<i class="icon material-symbols-outlined"> brush </i>
			User Interface
		</div>
		<div
			:class="{ active: activeId == 'code' }"
			@click="selectOption('code')"
		>
			<i class="icon material-symbols-outlined"> code </i>
			Code
			<span v-show="logEntryCount > 0" class="countLabel">{{
				logEntryCount
			}}</span>
		</div>
		<div
			v-if="isWorkflowsFeatureFlagged"
			:class="{ active: activeId == 'workflows' }"
			@click="selectOption('workflows')"
		>
			<i class="icon material-symbols-outlined"> linked_services </i>
			Workflows
		</div>
		<div
			:class="{ active: activeId == 'preview' }"
			@click="selectOption('preview')"
		>
			<i class="icon material-symbols-outlined"> preview </i>
			Preview
		</div>
	</div>
</template>

<script setup lang="ts">
import { computed, inject, Ref, ref } from "vue";
import injectionKeys from "../injectionKeys";

const wf = inject(injectionKeys.core);
const ssbm = inject(injectionKeys.builderManager);

const isWorkflowsFeatureFlagged = computed(() =>
	wf.featureFlags.value.includes("workflows"),
);

let selectedId: Ref<string> = ref(null);

const selectOption = (optionId: "ui" | "code" | "preview" | "workflows") => {
	selectedId.value = optionId;
	ssbm.setMode(optionId);
	if (ssbm.getMode() != "preview") return;
	ssbm.setSelection(null);
};

const activeId = computed(() => {
	if (selectedId.value) return selectedId.value;
	const first = "ui";
	return first;
});

const logEntryCount = computed(() => {
	return ssbm.getLogEntryCount();
});
</script>

<style scoped>
@import "./sharedStyles.css";

.BuilderSwitcher {
	background: var(--builderHeaderBackgroundHoleColor);
	color: white;
	font-size: 0.75rem;
	display: flex;
	gap: 0px;
	padding: 4px;
	border-radius: 8px;
	overflow: hidden;
}

.BuilderSwitcher div {
	padding: 4px 12px 4px 12px;
	cursor: pointer;
	display: flex;
	align-items: center;
}

.BuilderSwitcher .icon {
	margin-right: 8px;
}

.BuilderSwitcher div.active {
	background: var(--builderHeaderBackgroundColor);
	border-radius: 4px;
}
</style>

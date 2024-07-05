<template>
	<v-select
		:model-value="value"
		:disabled="disabled"
		:items="items"
		:placeholder="t('select_a_collection')"
		@update:model-value="$emit('input', $event)"
	/>
</template>

<script>
import { useStores } from '@directus/extensions-sdk';
import { computed } from 'vue';
import { useI18n } from 'vue-i18n';

export default {
	props: {
		value: {
			type: String,
			default: null,
		},
		disabled: {
			type: Boolean,
			default: null,
		},
	},
	emits: ['input'],
	setup() {
		const { t } = useI18n();
		const { useCollectionsStore } = useStores();
		const collectionsStore = useCollectionsStore();
		const collections = computed(() => {
			let collections = collectionsStore.collections;

			return [
				...collections.filter((collection) => collection.collection.startsWith('directus_') === false),
				...collectionsStore.crudSafeSystemCollections,
			];
		});

		const items = computed(() => {
			return collections.value.reduce((acc, collection) => {
				if (collection.type !== 'alias') {
					acc.push({
						text: collection.name,
						value: collection.collection,
					});
				}

				return acc;
			}, []);
		});

		return { items, t };
	},
};
</script>

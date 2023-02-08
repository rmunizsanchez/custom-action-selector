<template>
  <div v-if="filters.length > 0" class="flex flex-row">
      <component
          v-for="filter in filters"
          :resource-name="resourceName"
          :key="filter.name"
          :filter-key="filter.class"
          :is="filter.component"
          :lens="lens"
          @input="$emit('filter-changed')"
          @change="$emit('filter-changed')"
      />
  </div>
</template>

<script>
import { Filterable, InteractsWithQueryString } from 'laravel-nova'
export default {
  mixins: [ Filterable, InteractsWithQueryString ],
  props: {
    resourceName: String,
    lens: {
      type: String,
      default: '',
    },
    viaResource: String,
    viaHasOne: Boolean,
    softDeletes: Boolean,
    trashed: {
      type: String,
      validator: value => ['', 'with', 'only'].indexOf(value) != -1,
    },
    perPage: [String, Number],
    perPageOptions: Array,
  },
  methods: {
    trashedChanged(event) {
      this.$emit('trashed-changed', event.target.value)
    },
    perPageChanged(event) {
      this.$emit('per-page-changed', event.target.value)
    },
  },
  mounted() {
    this.$el.parentNode.parentNode.parentNode.parentNode.classList.add('flex-col')
  },
  computed: {
    /**
     * Get the name of the page query string variable.
     */
    pageParameter() {
      return this.viaRelationship
          ? this.viaRelationship + '_page'
          : this.resourceName + '_page'
    },
    /**
     * Return the filters from state
     */
    filters() {
      return this.$store.getters[`${this.resourceName}/filters`]
    },
    /**
     * Determine via state whether filters are applied
     */
    filtersAreApplied() {
      return this.$store.getters[`${this.resourceName}/filtersAreApplied`]
    },
    /**
     * Return the number of active filters
     */
    activeFilterCount() {
      return this.$store.getters[`${this.resourceName}/activeFilterCount`]
    }
  },
}
</script>
<template>
  <tags-input
    v-model="selectedOptions"
    :existing-tags="existingOptions"
    :typeahead="true"
    :typeahead-style="'dropdown'"
    :typeahead-max-results="maxResults"
    :typeahead-activation-threshold="activationThreshold"
    :placeholder="placeholder"
    :limit="1"
    :hide-input-on-limit="true"
    :only-existing-tags="onlyExistingTags"
    :typeahead-hide-discard="typeaheadHideDiscard"
    @change="updateExistingOptions"
    @tag-added="onTagAdded"
    @tag-removed="onTagRemoved"
  >
  </tags-input>
</template>

<script lang="ts">
import { Vue, Component, Prop, Watch } from 'vue-property-decorator';
import VoerroTagsInput from '@voerro/vue-tagsinput';
import '@voerro/vue-tagsinput/dist/style.css';
import T from '../../lang';
import { types } from '../../api_types';

@Component({
  components: {
    'tags-input': VoerroTagsInput,
  },
})
export default class Typeahead extends Vue {
  @Prop({ default: () => [] }) existingOptions!: types.ListItem[];
  @Prop({ default: () => [] }) options!: types.ListItem[];
  @Prop({ default: 3 }) activationThreshold!: number;
  @Prop({ default: 5 }) maxResults!: number;
  @Prop({ default: null }) value!: null | types.ListItem;
  @Prop({ default: true }) onlyExistingTags!: boolean;
  @Prop({ default: true }) typeaheadHideDiscard!: boolean;
  @Prop({ default: T.typeaheadSearchPlaceholder }) placeholder!: string;

  T = T;
  selectedOptions = this.options;

  updateExistingOptions(query: string): void {
    if (query.length < this.activationThreshold) return;
    this.$emit('update-existing-options', query);
  }

  onTagAdded(): void {
    if (this.selectedOptions.length < 1) return;
    this.$emit('update:value', this.selectedOptions[0]);
  }

  onTagRemoved(): void {
    this.$emit('update:value', null);
  }

  @Watch('value')
  onValueChanged(newValue: null | types.ListItem): void {
    if (!newValue) {
      this.selectedOptions = [];
      return;
    }
    this.existingOptions.push(newValue);
    this.selectedOptions = this.existingOptions.filter(
      (option) => option.key === newValue.key,
    );
  }
}
</script>

<style lang="scss">
@import '../../../../sass/main.scss';

.tags-input-remove::before,
.tags-input-remove::after,
.tags-input-typeahead-item-highlighted-default {
  background-color: $omegaup-primary--darker;
}

.tags-input-wrapper-default {
  height: 38px;
}
</style>

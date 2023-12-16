<template>
  <div>
    <h4>Submit Workflow</h4>
    <h5>{{ namespace }} / {{ name }}</h5>
    <ErrorNotice v-if="error" :error="error" />
    <div class="white-box">
      <div key="entrypoint" title="Entrypoint" style="margin-bottom: 25px;">
        <label>Entrypoint</label>
        <Select
          :value="entrypoint"
          :options="templateOptions"
          @change="selected => handleEntrypointChange(selected)"
        />
      </div>
      <div key="parameters" style="margin-bottom: 25px;">
        <label>Parameters</label>
        <ParametersInput
          v-if="workflowParameters.length > 0"
          :parameters="workflowParameters"
          @change="handleWorkflowParametersChange"
        />
        <ParametersInput
          v-if="parameters.length > 0"
          :parameters="parameters"
          @change="handleParametersChange"
        />
        <br v-if="workflowParameters.length === 0 && parameters.length === 0" />
        <label v-if="workflowParameters.length === 0 && parameters.length === 0">No parameters</label>
      </div>
      <div key="labels" style="margin-bottom: 25px;">
        <label>Labels</label>
        <TagsInput :tags="labels" @change="handleLabelsChange" />
      </div>
      <div key="submit">
        <button @click="submit" class="argo-button argo-button--base" :disabled="isSubmitting">
          <i class="fa fa-plus"></i>
          {{ isSubmitting ? 'Loading...' : 'Submit' }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { Select } from 'argo-ui';
import { reactive, computed, ref } from 'vue';
import { ParametersInput } from '../../shared/components/parameters-input/parameters-input';
import { TagsInput } from '../../shared/components/tags-input/tags-input';
import { services } from '../../shared/services';
import { Utils } from '../../shared/utils';

export default {
  props: {
    kind: String,
    namespace: String,
    name: String,
    entrypoint: String,
    templates: Array,
    workflowParameters: Array,
  },
  setup(props) {
    const workflowEntrypoint = '<default>';
    const defaultTemplate = {
      name: workflowEntrypoint,
      inputs: {
        parameters: [],
      },
    };

    const entrypoint = ref(workflowEntrypoint);
    const parameters = ref([]);
    const workflowParameters = ref(JSON.parse(JSON.stringify(props.workflowParameters)));
    const labels = ref(['submit-from-ui=true']);
    const error = ref(null);
    const isSubmitting = ref(false);

    const templates = computed(() => {
      return [defaultTemplate].concat(props.templates);
    });

    const templateOptions = computed(() => {
      return templates.value.map((t) => ({
        value: t.name,
        title: t.name,
      }));
    });

    async function submit() {
      isSubmitting.value = true;
      try {
        const submitted = await services.workflows.submit(props.kind, props.name, props.namespace, {
          entryPoint: entrypoint.value === workflowEntrypoint ? null : entrypoint.value,
          parameters: [
            ...workflowParameters.value
              .filter((p) => Utils.getValueFromParameter(p) !== undefined)
              .map((p) => p.name + '=' + Utils.getValueFromParameter(p)),
            ...parameters.value
              .filter((p) => Utils.getValueFromParameter(p) !== undefined)
              .map((p) => p.name + '=' + Utils.getValueFromParameter(p)),
          ],
          labels: labels.value.join(','),
        });
        document.location.href = uiUrl(`workflows/${submitted.metadata.namespace}/${submitted.metadata.name}`);
      } catch (err) {
        error.value = err;
        isSubmitting.value = false;
      }
    }

    function handleEntrypointChange(selected) {
      const selectedTemp = templates.value.find((t) => t.name === selected.value);
      entrypoint.value = selected.value;
      parameters.value = selectedTemp?.inputs?.parameters || [];
    }

    function handleWorkflowParametersChange(value) {
      workflowParameters.value = value;
    }

    function handleParametersChange(value) {
      parameters.value = value;
    }

    function handleLabelsChange(value) {
      labels.value = value;
    }

    return {
      entrypoint,
      parameters,
      workflowParameters,
      labels,
      error,
      isSubmitting,
      templates,
      templateOptions,
      submit,
      handleEntrypointChange,
      handleWorkflowParametersChange,
      handleParametersChange,
      handleLabelsChange,
    };
  },
};
</script>

<style scoped>
.white-box {
  background-color: #fff;
  border: 1px solid #ddd;
  padding: 20px;
  border-radius: 4px;
}
</style>

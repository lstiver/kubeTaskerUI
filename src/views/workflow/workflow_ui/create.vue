<template>
  <div>
    <div v-if="stage === 'choose-method'" class="white-box">
      <h4>Submit new workflow</h4>
      <p>Either:</p>
      <div style="margin: 10px; margin-left: 20px;">
        <select v-model="selectedWorkflowTemplate" @change="selectWorkflowTemplate">
          <option value="" disabled selected>Select a workflow template...</option>
          <option v-for="template in workflowTemplates" :key="template.metadata.name" :value="template.metadata.name">{{ template.metadata.name }}</option>
        </select>
      </div>
      <p>Or:</p>
      <div style="margin: 10px; margin-left: 20px;">
        <a @click="setStage('full-editor')">Edit using full workflow options <i class="fa fa-caret-right" /></a>
      </div>
    </div>
    <div v-else-if="stage === 'submit-workflow' && workflowTemplate">
      <SubmitWorkflowPanel
        :kind="'WorkflowTemplate'"
        :namespace="workflowTemplate.metadata.namespace"
        :name="workflowTemplate.metadata.name"
        :entrypoint="workflowTemplate.spec.entrypoint"
        :templates="workflowTemplate.spec.templates || []"
        :workflowParameters="workflowTemplate.spec.arguments.parameters || []"
      />
      <a @click="setStage('full-editor')">Edit using full workflow options <i class="fa fa-caret-right" /></a>
    </div>
    <div v-else-if="stage === 'full-editor' && workflow">
      <div>
        <UploadButton @onUpload="setWorkflow" @onError="setError" />
        <button @click="createWorkflow">Create</button>
      </div>
      <ErrorNotice :error="error" />
      <WorkflowEditor :template="workflow" @onChange="setWorkflow" @onError="setError" />
      <div>
        <ExampleManifests />.
      </div>
    </div>
  </div>
</template>

<script>
import { ref, watch, onMounted } from 'vue';
import { Workflow, WorkflowTemplate } from '../models';
import { services } from '../shared/services';
import { exampleWorkflow } from '../shared/examples';
import { Utils } from '../shared/utils';
import { SubmitWorkflowPanel } from './submit-workflow-panel';
import { WorkflowEditor } from './workflow-editor';

export default {
  props: {
    namespace: String,
    onCreate: Function
  },
  setup(props) {
    const workflowTemplates = ref([]);
    const selectedWorkflowTemplate = ref('');
    const workflowTemplate = ref(null);
    const stage = ref('choose-method');
    const workflow = ref(null);
    const error = ref(null);

    const setWorkflowTemplates = (templates) => {
      workflowTemplates.value = templates;
    };

    const setWorkflowTemplate = (template) => {
      workflowTemplate.value = template;
    };

    const setStage = (newStage) => {
      stage.value = newStage;
    };

    const setWorkflow = (newWorkflow) => {
      workflow.value = newWorkflow;
    };

    const setError = (err) => {
      error.value = err;
    };

    const selectWorkflowTemplate = (event) => {
      const templateName = event.target.value;
      const selectedTemplate = workflowTemplates.value.find(template => template.metadata.name === templateName);
      setWorkflowTemplate(selectedTemplate);
    };

    const createWorkflow = async () => {
      try {
        const newWorkflow = await services.workflows.create(workflow.value, Utils.getNamespaceWithDefault(workflow.value.metadata.namespace));
        props.onCreate(newWorkflow);
      } catch (err) {
        setError(err);
      }
    };

    watch(() => stage.value, (newStage) => {
      if (newStage === 'full-editor') {
        if (workflowTemplate.value) {
          setWorkflow({
            metadata: {
              generateName: `${workflowTemplate.value.metadata.name}-`,
              namespace: props.namespace,
              labels: {
                'workflows.argoproj.io/workflow-template': workflowTemplate.value.metadata.name,
                'submit-from-ui': 'true'
              }
            },
            spec: {
              arguments: workflowTemplate.value.spec.arguments,
              workflowTemplateRef: {
                name: workflowTemplate.value.metadata.name
              }
            }
          });
        } else {
          setWorkflow(exampleWorkflow(Utils.getNamespaceWithDefault(props.namespace)));
        }
      }
    });

    watch(() => workflowTemplate.value, (newTemplate) => {
      if (newTemplate) {
        setStage('submit-workflow');
      }
    });

    onMounted(() => {
      services.workflowTemplate
        .list(props.namespace, [])
        .then(list => list.items || [])
        .then(setWorkflowTemplates)
        .catch(setError);
    });

    return {
      workflowTemplates,
      selectedWorkflowTemplate,
      stage,
      workflowTemplate,
      workflow,
      error,
      setStage,
      setWorkflow,
      setError,
      selectWorkflowTemplate,
      createWorkflow
    };
  }
};
</script>

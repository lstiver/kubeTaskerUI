<template>
  <div>
    <Tabs
      :navTransparent="true"
      :selectedTabKey="selectedTabKey"
      @onTabSelected="onTabSelected"
      :tabs="tabs"
    />
  </div>
</template>

<script>
import { defineComponent } from 'vue';
import { Tabs } from 'argo-ui';
import { Workflow } from '../../../models';
import { MetadataEditor } from '../../shared/components/editors/metadata-editor';
import { WorkflowParametersEditor } from '../../shared/components/editors/workflow-parameters-editor';
import { ObjectEditor } from '../../shared/components/object-editor/object-editor';

export default defineComponent({
  name: 'WorkflowEditor',
  props: {
    template: {
      type: Object,
      required: true,
    },
    onChange: {
      type: Function,
      required: true,
    },
    onError: {
      type: Function,
      required: true,
    },
    onTabSelected: {
      type: Function,
    },
    selectedTabKey: {
      type: String,
    },
  },
  computed: {
    tabs() {
      return [
        {
          key: 'manifest',
          title: 'Manifest',
          content: (
            <ObjectEditor
              type="io.argoproj.workflow.v1alpha1.Workflow"
              :value="template"
              @onChange={(x) => this.onChange({ ...x })}
            />
          ),
        },
        {
          key: 'parameters',
          title: 'Parameters',
          content: (
            <WorkflowParametersEditor
              :value="template.spec"
              @onChange={(spec) => this.onChange({ ...template, spec })}
              @onError="onError"
            />
          ),
        },
        {
          key: 'metadata',
          title: 'MetaData',
          content: (
            <MetadataEditor
              :value="template.metadata"
              @onChange={(metadata) => this.onChange({ ...template, metadata })}
            />
          ),
        },
      ];
    },
  },
});
</script>

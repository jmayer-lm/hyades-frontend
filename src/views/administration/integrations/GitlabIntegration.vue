<template>
  <b-card no-body :header="header">
    <b-card-body>
      <c-switch
        id="enabled"
        color="primary"
        v-model="enabled"
        label
        v-bind="labelIcon"
      />{{ $t('admin.integration_gitlab_enable') }}
      <b-validated-input-group-form-input
        id="Gitlab-Host"
        :label="$t('admin.gitlab_host')"
        input-group-size="mb-3"
        rules="required"
        type="text"
        v-model="host"
        lazy="true"
      />

      <b-validated-input-group-form-input
        id="Group-Name"
        :label="$t('Group Name')"
        input-group-size="mb-3"
        rules="required"
        type="text"
        v-model="groupName"
        lazy="true"
      />

      <b-validated-input-group-form-input
        id="Gitlab-Integration-apiKey"
        :label="$t('GITLAB API TOKEN')"
        input-group-size="mb-3"
        rules="required"
        type="password"
        v-model="apiKey"
        lazy="true"
      />
    </b-card-body>
    <b-card-footer>
      <b-button variant="outline-primary" class="px-4" @click="saveChanges">{{
        $t('message.update')
      }}</b-button>
    </b-card-footer>
  </b-card>
</template>

<script>
import { Switch as cSwitch } from '@coreui/vue';
import BValidatedInputGroupFormInput from '../../../forms/BValidatedInputGroupFormInput';
// eslint-disable-next-line no-unused-vars
import axios from 'axios'; // Import axios
import common from '../../../shared/common';
import configPropertyMixin from '../mixins/configPropertyMixin';

export default {
  mixins: [configPropertyMixin],
  props: {
    header: String,
  },
  components: {
    cSwitch,
    BValidatedInputGroupFormInput,
  },
  data() {
    return {
      enabled: false,
      host: '',
      url: '',
      apiKey: '',
    };
  },
  methods: {
    saveChanges: function () {
      try {
        this.updateConfigProperties([
          {
            groupName: 'integrations',
            propertyName: 'gitlab_integration.enabled',
            propertyValue: this.enabled,
          },
          {
            groupName: 'integrations',
            propertyName: 'gitlab_integration.host',
            propertyValue: this.host,
          },
          {
            groupName: 'integrations',
            propertyName: 'gitlab_integration.url',
            propertyValue: this.url,
          },
          {
            groupName: 'integrations',
            propertyName: 'gitlab_integration.apiKey',
            propertyValue: this.apiKey,
          },
        ]);
      } catch (error) {
        console.error('Error updating configuration properties:', error);
      }
    },
  },
  created() {
    this.axios
      .get(this.configUrl)
      .then((response) => {
        let configItems = response.data.filter(function (item) {
          return item.groupName === 'integrations';
        });
        for (let i = 0; i < configItems.length; i++) {
          let item = configItems[i];
          switch (item.propertyName) {
            case 'gitlab_integration.enabled':
              this.enabled = common.toBoolean(item.propertyValue);
              break;
            case 'gitlab_integration.host':
              this.host = item.propertyValue;
              break;
            case 'gitlab_integration.url':
              this.url = item.propertyValue;
              break;
            case 'gitlab_integration.apiKey':
              this.apiKey = item.propertyValue;
              break;
          }
        }
      })
      .catch((error) => {
        console.error('Error fetching configuration data:', error);
      });
  },
};
</script>

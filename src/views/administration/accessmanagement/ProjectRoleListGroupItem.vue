<template>
  <b-list-group-item class="flex-column align-items-start">
    <div class="d-flex justify-content-between">
      <span class="text-monospace">{{ projectRole.role.name }}</span>
      <div class="d-flex">
        <!-- Button to open the SelectRoleModal -->
        <b-button
          size="sm"
          class="action-icon"
          v-b-tooltip.hover
          :title="$t('admin.edit_role')"
          v-on:click="openSelectRoleModal"
        >
          <span class="fa fa-edit"></span>
        </b-button>
        <b-button
          size="sm"
          class="action-icon ml-3"
          v-on:click="$emit('removeClicked')"
          v-b-tooltip.hover
          :title="$t('admin.remove_role')"
        >
          <span class="fa fa-trash-o"></span>
        </b-button>
      </div>
    </div>
    <p class="mt-2 font-weight-light">{{ projectRole.project.name }}</p>

   <!-- Include the SelectRoleModal -->
   <select-role-modal
      v-model="showSelectRoleModal"
      :username="username"
      :currentRole="projectRole.role.uuid"
      :currentProject="projectRole.project.uuid"
      @selection="handleRoleSelection"
      @close="showSelectRoleModal = false"
    />
  </b-list-group-item>
</template>

<script>
import SelectRoleModal from './SelectRoleModal.vue';

export default {
props: {
  projectRole: {
    type: Object,
    required: true,
  },
  username: {
    type: String,
    required: true,
    },
},
mounted() {
  console.log('Username in ProjectRoleListGroupItem:', this.username);
  console.log('Initial value of showSelectRoleModal:', this.showSelectRoleModal);
},
  components: {
    SelectRoleModal,
  },
  data() {
    return {
      showSelectRoleModal: false,
    };
  },
methods: {
  openSelectRoleModal() {
    console.log('Opening SelectRoleModal')
    this.$root.$emit('bv::show::modal', 'selectRoleModal');
  },
  handleRoleSelection(selection) {
    // Log the selected role and project for debugging
    console.log('Selected Role:', selection.role);
    console.log('Selected Project:', selection.project);

    // Prepare the payload for the backend
    const payload = {
      roleUuid: selection.role,
      projectUuid: selection.project,
    };

    // Make an API call to update the role
    const url = `${this.$api.BASE_URL}/${this.$api.URL_ROLE}/${this.projectRole.role.uuid}`;
    this.axios
      .put(url, payload)
      .then(() => {
        this.$toastr.s(this.$t('message.updated')); // Show success message
        this.$emit('roleUpdated', selection); // Notify parent of the update
        this.showSelectRoleModal = false; // Close the modal
      })
      .catch((error) => {
        console.error('Error updating role:', error);
        this.$toastr.w(this.$t('condition.unsuccessful_action')); // Show error message
      });
    },
  },
}
</script>

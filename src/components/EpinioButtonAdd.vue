<template>
  <button
    class="btn role-primary icon-btn btn-sm"
    type="button"
    aria-label="Add an Application"
    @click="click"
  >
    <img src="@/assets/images/epinio.svg">
  </button>
</template>

<script lang="ts">
import { ipcRenderer } from 'electron';
import Vue from 'vue';

export default Vue.extend({
  name: 'epinio-button-add',

  data: {
    installed: false,
  },

  methods: {
    async click() {
      console.info('epinio clicked');

      if (!this.installed) {
        const options = {
          message:   'Install Epinio?',
          type:      'question',
          buttons:   ['Yes', 'No'],
          defaultId: 1,
          title:     'Confirming Epinio Installation',
          cancelId:  1
        };
        const result = await ipcRenderer.invoke('show-message-box', options);
        if (result.response === 1) {
          return;
        }
        this.installed = true;
        ipcRenderer.invoke('epinio-install');

      } else {
        const options = {
          message:   'Deploy Application',
          properties: ['openDirectory'],
        };
        const result = await ipcRenderer.invoke('open-file-dialog', options);

        if (result.filePaths.length > 0) {
          console.info(`deploy ${result.filePaths[0]} `);
          ipcRenderer.invoke('epinio-push', result.filePaths[0]);
        }
      }

    }
  }
});
</script>

<style lang="scss" scoped>
button.btn {
  background-color: #fff;
  display: flex;
  width: 32px;
  height: 32px;
  &:hover {
    color: #fff ;
  }
}

img {
    height: 32px;
    padding: 0px 0px -5px 10px; 
}
</style>

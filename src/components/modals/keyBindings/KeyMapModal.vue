<template>
  <Modal id="keymap-modal"
         :value="isVisible"
         title="Key Bindings"
         @on-visible-change="visibleChange"
  >
    <div class="keymap">
      <WindowsKeys v-if="isWindows || isLinux"/>
      <MacKeys v-if="isMac"/>
    </div>
    <div slot="footer">
      <Button type="text" @click="resetToDefaults">Reset to defaults</Button>
      <Button size="large" @click="closeModal">Close</Button>
    </div>
  </Modal>
</template>

<script>
  import WindowsKeys from './WindowsKeys';
  import MacKeys from './MacKeys';

  export default {
    name: 'KeyMapModal',
    components: {MacKeys, WindowsKeys},
    computed: {
      isVisible () {
        return this.$store.state.modals.keymap.isVisible;
      },
      system () {
        return this.$store.state.modals.keymap.system;
      },
      isWindows () {
        return this.system.includes('win');
      },
      isMac () {
        return this.system.includes('mac');
      },
      isLinux () {
        return this.system.includes('linux');
      }
    },
    methods: {
      resetToDefaults () {
        this.$Modal.confirm({
          title: `Reset key bindings to defaults`,
          okText: 'Reset',
          cancelText: 'Cancel',
          closable: true,
          content: `<p>You are going to reset key bindings to default values.</p>
                    <p>Are you sure you want to continue?</p>`,
          onOk: () => {
            this.$store.dispatch('resetKeyBindings');
            this.$store.dispatch('fetchSettings');
            this.$Message.success('Key bindings cleared out');
          }
        });
      },
      visibleChange (isVisible) {
        if (!isVisible) {
          this.closeModal();
        }
      },
      closeModal () {
        this.$store.dispatch('hideKeymapModal');
      }
    }
  };
</script>

<style>

  .close-icon {
    position: absolute;
    right: 16px;
    top: 16px;
    cursor: pointer;
  }

  table {
    width: 100%;
    border-collapse: collapse;
  }

  thead {
    font-weight: bold;
    border-bottom: 1px solid #eaeaea;
    margin-bottom: 4px;
  }

  thead td {
    padding: 4px 0;
  }

  .header {
    margin-bottom: 16px;
  }

  tbody tr {
    border-bottom: 1px solid #f3f3f3;
    cursor: pointer;
    border-radius: 4px;
    padding: 4px;
    transition: all .3s;
  }

  tbody tr:hover {
    background-color: #f3f3f3;
  }

  .keymap {
    position: relative;
    width: 500px;
    border-radius: 4px;
    padding: 8px;
  }
</style>

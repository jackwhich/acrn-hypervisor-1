<template>
  <div class="virtio_networks" v-if="defaultVal && defaultVal.length>0">
    <div class="virtio_network" v-for="(network, index) in defaultVal">
      <div class="virtio_network_demo">
        <b style="margin-bottom: 2rem">Virtio network device</b>
        <b-row class="align-items-center my-2 mt-4">
          <b-col md="2">
            <label>Virtio framework: </label>
          </b-col>
          <b-col md="4">
            <b-form-select v-model="network.virtio_framework" :options="NetworkFrameworkType"/>
          </b-col>
        </b-row>

        <b-row class="align-items-center my-2">
          <b-col md="2">
            <label>Network interface name: </label>
          </b-col>
          <b-col md="4">
            <b-form-input v-model="network.interface_name"/>
          </b-col>
        </b-row>
      </div>
      <div class="ToolSet">
        <div>
          <Icon size="18px" color="rgb(60,60,60)" @click="removeVirtioNetwork(index)">
            <Minus/>
          </Icon>
        </div>
        <div>
          <Icon size="18px" color="rgb(60,60,60)" @click="addVirtioNetwork">
            <Plus/>
          </Icon>
        </div>
      </div>

    </div>

  </div>
  <div v-else>
    <b style="margin-bottom: 2rem">Virtio network device</b>
    <div class="ToolSet">
      <div @click="addVirtioNetwork">
        <Icon size="18px">
          <Plus/>
        </Icon>
      </div>
    </div>
  </div>
</template>

<script>
import _ from "lodash";
import {Icon} from "@vicons/utils";
import {Plus, Minus} from '@vicons/fa'
import {fieldProps, vueUtils} from '@lljj/vue3-form-naive';

export default {
  name: "Network",
  components: {Icon, Plus, Minus},
  props: {
    ...fieldProps,
    fieldProps: {
      type: null,
      default: null
    }
  },
  data() {
    return {
      NetworkFrameworkType: this.rootSchema.definitions['VirtioNetworkFrameworkType']['enum'],
      NetworkFrameworkDefault: this.rootSchema.definitions['VirtioNetworkConfiguration']['properties']['virtio_framework']['default'],
      defaultVal: vueUtils.getPathVal(this.rootFormData, this.curNodePath)
    };
  },
  watch: {
    rootFormData: {
      handler(newValue, oldValue) {
        this.defaultVal = vueUtils.getPathVal(newValue, this.curNodePath)
      },
      deep: true
    },
    defaultVal: {
      handler(newValue, oldValue) {
        // Note: `newValue` will be equal to `oldValue` here
        // on nested mutations as long as the object itself
        // hasn't been replaced.
        vueUtils.setPathVal(this.rootFormData, this.curNodePath, newValue);
      },
      deep: true
    }
  },
  methods: {
    removeVirtioNetwork(index) {
      this.defaultVal.splice(index, 1);
    },
    addVirtioNetwork() {
      if (!_.isArray(this.defaultVal)) {
        this.defaultVal = []
      }
      this.defaultVal.push({
        "virtio_framework": this.NetworkFrameworkDefault,
        "interface_name": "",
      })
    }
  }
}
</script>

<style scoped>
label:after{
  content: '*';
  color: red;
}
.ToolSet {
  display: flex;
  flex-direction: row-reverse;
  gap: 8px;
}
.ToolSet div{
  padding: 8px;
  border: 1px solid rgb(193,193,193);
  border-radius: 5px;
}
.virtio_networks{
  width: 100%;
}
.virtio_network {
  width: 100%;
  display: flex;
  gap: 2rem;
  align-items: end;
}

.virtio_network_demo {
  width: 100%;
  border: 2px solid #cccccc;
  padding: 8px 0 12px 6px;
  border-radius: 5px;
  margin-bottom: 1rem;
}
</style>
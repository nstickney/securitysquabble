<template>
  <div>
    <div>
      <h3>Expert Security Advice?</h3>
      <draggable element="span" v-model="list2" :options="dragOptions" :move="onMove">
        <transition-group name="no" class="list-group" tag="ul">
          <li class="list-group-item" v-for="element in list2" :key="element.order">
            {{element.name}}
          </li>
        </transition-group>
      </draggable>
    </div>

    <div>
      <h3>Security Advice</h3>
      <draggable class="list-group" element="ul" v-model="list" :options="dragOptions" :move="onMove" @start="isDragging=true" @end="isDragging=false">
        <transition-group type="transition" :name="'flip-list'">
          <li class="list-group-item" v-for="element in list" :key="element.order">
            {{element.name}}
          </li>
        </transition-group>
      </draggable>
    </div>

    <button type="button" class="btn btn-default" @click="resetLists">Reset</button>
  </div>
</template>

<script>
import draggable from "vuedraggable";
const message = [
  "Use antivirus software",
  "Use strong passwords",
  "Change passwords frequently",
  "Only visit websites you know",
  "Don't share personal information",
  "Install software updates",
  "Use unique passwords",
  "Use two-factor authentication",
  "Use a password manager"
];

export default {
  name: "SecuritySquabble",
  components: {
    draggable
  },
  data() {
    return {
      list: message.map((name, index) => {
        return { name, order: index + 1, fixed: false };
      }),
      list2: [],
      editable: true,
      isDragging: false,
      delayedDragging: false
    };
  },
  methods: {
    resetLists() {
      this.list = message.map((name, index) => {
        return { name, order: index + 1, fixed: false };
      })
      this.list2 = []
    },
    orderList() {
      this.list = this.list.sort((one, two) => {
        return one.order - two.order;
      });
    },
    onMove({ relatedContext, draggedContext }) {
      const relatedElement = relatedContext.element;
      const draggedElement = draggedContext.element;
      return (
              (!relatedElement || !relatedElement.fixed) && !draggedElement.fixed
      );
    }
  },
  computed: {
    dragOptions() {
      return {
        animation: 0,
        group: "description",
        disabled: !this.editable,
        ghostClass: "ghost"
      };
    },
    listString() {
      return JSON.stringify(this.list, null, 2);
    },
    list2String() {
      return JSON.stringify(this.list2, null, 2);
    }
  },
  watch: {
    isDragging(newValue) {
      if (newValue) {
        this.delayedDragging = true;
        return;
      }
      this.$nextTick(() => {
        this.delayedDragging = false;
      });
    }
  }
};
</script>

<style>
button {
  font-weight: bold;
  margin: 3em;
}

.flip-list-move {
  transition: transform 0.5s;
}

.no-move {
  transition: transform 0s;
}

.ghost {
  opacity: 0.5;
  background: #c8ebfb;
}

.list-group {
  list-style: none;
  min-height: 3em;
  padding: 0;
}

.list-group-item {
  border: 1px solid black;
  border-radius: 0.5em;
  cursor: move;
  margin: 0.25em;
  padding: 0.25em;
}

.list-group-item i {
  cursor: pointer;
}
</style>

<template>
  <div class="squabble">
    <div class="row">
      <div>
        <h3>Questionable Online Security Advice</h3>
        <draggable class="list-group" element="ul" v-model="list" :options="dragOptions" :move="onMove" @start="isDragging=true" @end="isDragging=false">
        <transition-group type="transition" :name="'flip-list'">
          <li class="list-group-item" v-for="element in list" :key="element.order">
            {{element.name}}
          </li>
        </transition-group>
        </draggable>
      </div>

      <div>
        <h3>Security Experts' Top Online Safety Practices</h3>
        <draggable element="span" v-model="list2" :options="dragOptions" :move="onMove">
        <transition-group name="no" class="list-group" tag="ul">
          <li class="list-group-item" v-for="element in list2" :key="element.order">
            {{element.name}}
          </li>
        </transition-group>
        </draggable>
      </div>
    </div>

    <div class="instructions">
      <div class="text">
        Drag and drop five items from the list on the left to the list on the right. Order your five selections by decreasing importance. When you are satisfied, click the "Score" button to see your score. If you want a clean slate, click the "Reset" button.
      </div>
      <button type="button" @click="resetLists">Reset</button>
      <button type="button" @click="calcScore" :disabled="score > 0">Score</button>
      <h1 v-if="score > 0">Your Score: {{score}}</h1>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";

// https://security.googleblog.com/2015/07/new-research-comparing-how-security.html
const message = [
  "Install software updates",
  "Use unique passwords",
  "Use two-factor authentication",
  "Use strong passwords",
  "Use a password manager",
  "Use antivirus software",
  "Change passwords frequently",
  "Only visit websites you know",
  "Don't share personal information",
];

// https://stackoverflow.com/questions/2450954/how-to-randomize-shuffle-a-javascript-array
function fisherYates( array ){
  var count = array.length,
    randomnumber,
    temp;
  while( count ){
    randomnumber = Math.random() * count-- | 0;
    temp = array[count];
    array[count] = array[randomnumber];
    array[randomnumber] = temp
  }
}

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
      delayedDragging: false,
      score: 0
    };
  },
  methods: {
    calcScore() {
      this.score = 0
      for (var i=0, len=this.list2.length; i < len; i++) {
        if (this.list2[i].order < 5) {
          this.score += 5 - Math.abs(this.list2[i].order - (i + 1))
        }
      }
    },
    onMove({ relatedContext, draggedContext }) {
      const relatedElement = relatedContext.element;
      const draggedElement = draggedContext.element;
      return (
              (!relatedElement || !relatedElement.fixed) && !draggedElement.fixed
      );
    },
    resetLists() {
      this.score = 0
      this.list = message.map((name, index) => {
        return { name, order: index + 1, fixed: false };
      })
      this.list2 = []
      fisherYates(this.list)
    }
  },
  beforeMount() {
    fisherYates(this.list)
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
  margin: 3rem;
}

h3 {
  background: #f2bf30;
  border: 2px solid black;
  color: #000000;
  margin: 0;
  padding: 1rem;
}

.flip-list-move {
  transition: transform 0.5s;
}

.ghost {
  opacity: 0.5;
  background: #c8ebfb;
}

.instructions {
  padding: 3rem 0;
}

.list-group {
  list-style: none;
  margin: 0;
  min-height: 29.25rem;
  padding: 0;
}

.list-group-item {
  border-left: 2px solid black;
  border-right: 2px solid black;
  border-bottom: 2px solid black;
  cursor: move;
  margin: 0;
  padding: 1rem 0.5rem;
}

.list-group-item i {
  cursor: pointer;
}

.no-move {
  transition: transform 0s;
}

.row {
  display: flex;
}

.row > div {
  background: #eeeeee;
  flex: 50%;
  margin: 1rem;
}

.squabble {
  text-transform: uppercase;
}

.text {
  margin: 0 auto;
  max-width: 40em;
  text-align: left;
}

</style>

// vim: ai ts=2 sts=2 et sw=2

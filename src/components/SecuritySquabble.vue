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
        <draggable element="span" v-model="list2" :options="dragOptions2" :move="onMove">
        <transition-group name="no" class="list-group" tag="ul">
          <li class="list-group-item" v-for="element in list2" :key="element.order">
            {{element.name}}
          </li>
        </transition-group>
        </draggable>
      </div>
    </div>

    <div class="instructions">
      <h4>Instructions:</h4>
      Drag and drop five items from the <em>Questionable Online Security Advice</em> list to the <em>Security Experts' Top Online Safety Practices</em> list. Order your five selections by decreasing importance (most important at the top). When you are satisfied, click the "Score" button to see your score. If you want a clean slate, click the "Reset" button.
    </div>
    <button type="button" @click="resetLists">Reset</button>
    <button type="button" @click="calcScore" :disabled="!scoreable">Score</button>
    <h1 v-if="score > 0">Score: {{score}} out of 25</h1>
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
        if (this.list2[i].order < 6) {
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
      this.list2 = [];
      fisherYates(this.list);
    }
  },
  beforeMount() {
    fisherYates(this.list)
  },
  computed: {
    dragOptions() {
      return {
        animation: 0,
        group: {
          name: "Squabble",
          pull: this.list2.length < 5
        },
        disabled: !this.editable,
        ghostClass: "ghost"
      };
    },
    dragOptions2() {
      return {
        animation: 0,
        group: {
          name: "Squabble",
          put: this.list2.length < 5
        },
        disabled: !this.editable,
        ghostClass: "ghost"
      };
    },
    scoreable() {
      return this.score == 0 && this.list2.length == 5;
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
* {
  margin: 0;
  padding: 0;
}

button {
  font-weight: bold;
  margin: 1rem;
  text-transform: uppercase;
}

h1 {
  padding: 0 1rem 1rem;
}

h3 {
  align-items: center;
  background: #f2bf30;
  border: 2px solid black;
  display: flex;
  justify-content: center;
  min-height: 4em;
  padding: 0 1rem;
  text-transform: uppercase;
}

h4 {
  padding: 0.5rem 0;
}

.flip-list-move {
  transition: transform 0.5s;
}

.ghost {
  background: #c8ebfb;
  opacity: 0.5;
}

.instructions {
  padding: 0 1rem;
  margin: 0 auto;
  max-width: 35em;
  text-align: left;
}

.list-group {
  border-left: 2px dashed black;
  border-right: 2px dashed black;
  border-bottom: 2px dashed black;
  list-style: none;
  min-height: 3rem;
}

.list-group-item {
  border: 2px solid black;
  cursor: move;
  margin: -2px;
  padding: 1rem 0.5rem;
  text-transform: uppercase;
}

.list-group-item i {
  cursor: pointer;
}

.no-move {
  transition: transform 0s;
}

.row {
  padding: 0.5rem;
}

.row::after {
  content: "";
  clear: both;
  display: table;
}

.row > div {
  float: left;
  margin: 0.5rem;
  width: calc(100% - 1rem);
}

.squabble {
  background: #eeeeee;
}

@media only screen and (min-width: 768px) {
  .list-group {
    height: 504px;
    margin-top: -2px;
  }
  .row > div {
    width: calc(50% - 1rem);
  }
}
</style>

// vim: ai ts=2 sts=2 et sw=2

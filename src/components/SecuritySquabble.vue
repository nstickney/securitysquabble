<template>
  <div class="app">
    <div class="squabble">
      <div class="row">
        <div>
          <h3 class="flex">Questionable Online Security Advice</h3>
          <draggable class="list-group force-height" element="ul" v-model="list" :options="dragOptions" @start="isDragging=true" @end="isDragging=false">
          <transition-group type="transition" :name="'flip-list'">
            <li class="list-group-item" v-for="element in list" :key="element.order">
              <img :src="element.order + '.jpg'" />
              <div>{{element.name}}</div>
            </li>
          </transition-group>
          </draggable>
        </div>

        <div>
          <h3 class="flex">Top Online Safety Practices</h3>
          <draggable element="span" v-model="list2" :options="dragOptions2">
          <transition-group name="no" class="list-group force-height" tag="ul">
            <li class="list-group-item" v-for="element in list2" :key="element.order">
              <img :src="element.order + '.jpg'" />
              <div>{{element.name}}</div>
            </li>
          </transition-group>
          </draggable>
        </div>
      </div>

      <div class="instructions">
        <h4>Instructions:</h4>
        <p>Drag and drop five items from the <em>Questionable Online Security Advice</em> list to the <em>Top Online Safety Practices</em> list. Order your five selections by decreasing importance (most important at the top). When you are satisfied, click the "Score" button to see your score. If you want a clean slate, click the "Reset" button.</p>
      </div>
      <div class="buttons">
        <button type="button" @click="resetLists">Reset</button>
        <button type="button" @click="calcScore" :disabled="!scoreable">Score</button>
      </div>
      <div class="buttons">
        See the code on <a href="https://gitlab.com/nstickney/securitysquabble">GitLab</a>.
      </div>

      <div class="overlay" v-if="score > 0">
        <div class="row">
          <div>
            <h4>Your Score:</h4>
            <h3 class="flex">{{score}} out of 25</h3>
            <p>For more information, check out the <a href="https://security.googleblog.com/2015/07/new-research-comparing-how-security.html">Google Security blog post</a> that inspired this game, or <a href="https://www.us-cert.gov/ncas/tips/ST04-003">this article</a> from the United States Computer Emergency Readiness Team (US-CERT)'s <a href="https://www.us-cert.gov/ncas/tips">tips list</a>. You can also click on any of the advice in the <em>Security Experts' Top Online Safety Practices</em> chart to learn more about that topic.</p>
          </div>
          <div>
            <h3 class="flex">Security Experts' Top Online Safety Practices</h3>
            <ul class="list-group">
              <li class="list-group-item" v-for="element in experts" :key="element.order">
                <img :src="element.order + '.jpg'" />
                <div><a v-bind:href="element.link">{{element.name}}</a></div>
              </li>
            </ul>
          </div>
        </div>
        <div class="buttons flex">
          <button type="button" @click="resetLists">Play Again</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";

// https://security.googleblog.com/2015/07/new-research-comparing-how-security.html
const correct = [
  "Install software updates",
  "Use unique passwords",
  "Use two-factor authentication",
  "Use strong passwords",
  "Use a password manager"
];
const links = [
  "https://www.us-cert.gov/ncas/tips/ST04-006",
  "https://haveibeenpwned.com/Passwords",
  "https://twofactorauth.org/",
  "https://explainxkcd.com/wiki/index.php/936:_Password_Strength",
  "https://www.theverge.com/2017/7/24/15921282/best-password-manager-1password-lastpass-dashlane-how-to"
];
const incorrect = [
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
      list: correct.concat(incorrect).map((name, index) => {
        return { name, order: index + 1 };
      }),
      list2: [],
      experts: correct.map((name, index) => {
        return { name, order: index + 1, link: links[index] };
      }),
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
    resetLists() {
      this.score = 0
      this.list = correct.concat(incorrect).map((name, index) => {
        return { name, order: index + 1 };
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
  color: #111;
  margin: 0;
  padding: 0;
}

a, a:visited {
  color: #3131f2;
  text-decoration: none;
}

a:hover {
  color: #3161f2;
  text-decoration: underline;
}

button {
  font-weight: bold;
  margin: 1rem;
  text-transform: uppercase;
}

h3 {
  background: #f2bf30;
  border: 2px solid black;
  min-height: 4em;
  padding: 0 1rem;
  text-align: center;
  text-transform: uppercase;
}

h4 {
  padding: 0.5rem 0 0.25rem;
  text-transform: uppercase;
}

li {
  background: #eee;
  border: 2px solid black;
  cursor: move;
  margin: -2px;
  text-transform: uppercase;
}

li i {
  cursor: pointer;
}

li:hover {
  background: #fff;
}

li > div {
  align-items: center;
  display: flex;
  height: 3rem;
  padding: 0 1rem;
  width: calc(100% - 5rem);
}

li a, li a:hover, li a:visited {
  color: #111;
  text-decoration: none;
}

li > img {
  float: left;
  height: 3rem;
}
li::after {
  clear: both;
  content: "";
  display: table;
}

p {
  padding: 0.25rem 0 0.5rem;
  text-indent: 2rem;
}

ul {
  border-left: 2px dashed black;
  border-right: 2px dashed black;
  border-bottom: 2px dashed black;
  list-style: none;
}

.app {
  background: repeating-linear-gradient(
  135deg,
  #f2bf30,
  #f2bf30 2rem,
  black 2rem,
  black 4rem
  );
  box-shadow: 0.2rem 0.2rem 0.1rem 0.1rem;
  padding: 1rem;
  margin: 0 auto;
  max-width: 64rem;
}

.buttons {
  text-align: center;
}

.flex {
  display: flex;
  justify-content: center;
  align-items: center;
}

.flip-list-move {
  transition: transform 0.5s;
}

.force-height {
  min-height: 10rem;
}

.ghost {
  background: #c8ebfb;
  opacity: 0.5;
}

.instructions {
  margin: 0 auto;
  max-width: 36rem;
  padding: 0 1rem;
}

.no-move {
  transition: transform 0s;
}

.overlay {
  background: rgba(238, 238, 238, 0.95);
  height: 100%;
  left: 0;
  position: absolute;
  width: 100%;
  top: 0px;
  z-index: 1000;
}

.overlay > div {
  margin: 0 auto;
  max-width: 64rem;
}

.row {
  margin: 0 auto;
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
  background: #eee;
  position: relative;
}

@media only screen and (min-width: 768px) {
  .force-height {
    height: calc(27rem + 18px);
    margin-top: -2px;
  }
  .row > div {
    width: calc(50% - 1rem);
  }
}
</style>

// vim: ai ts=2 sts=2 et sw=2

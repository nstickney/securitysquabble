<template>
  <div class="app">
    <div class="squabble">
      <div class="row">
        <div>
          <h3 class="flex">Questionable Online Security Advice</h3>
          <draggable
            class="list-group force-height"
            tag="ul"
            v-model="choices"
            :group="{ name: 'Squabble', pull: answers.length < 5 }"
            :animation="0"
            ghost-class="ghost"
            item-key="order"
          >
            <template #item="{ element }">
              <li class="list-group-item">
                <img :src="'images/' + element.order + '.jpg'" />
                <div>{{ element.name }}</div>
              </li>
            </template>
          </draggable>
        </div>

        <div>
          <h3 class="flex">Top Online Safety Practices</h3>
          <draggable
            class="list-group force-height"
            tag="ul"
            v-model="answers"
            :group="{ name: 'Squabble', put: answers.length < 5 }"
            :animation="0"
            ghost-class="ghost"
            item-key="order"
          >
            <template #item="{ element }">
              <li class="list-group-item">
                <img :src="'images/' + element.order + '.jpg'" />
                <div>{{ element.name }}</div>
              </li>
            </template>
          </draggable>
        </div>
      </div>

      <div class="instructions">
        <h4>Instructions:</h4>
        <p>Drag and drop five items from the <em>Questionable Online Security Advice</em> list to the <em>Top Online Safety Practices</em> list. Order your five selections by decreasing importance (most important at the top). When you are satisfied, click the "Score" button to see your score. If you want a clean slate, click the "Reset" button.</p>
      </div>
      <div class="buttons">
        <button type="button" @click="reset">Reset</button>
        <button type="button" @click="submitScore" :disabled="!scoreable">Score</button>
      </div>
      <div class="buttons">
        See the code on <a href="https://git.sr.ht/~stick/squabble">SourceHut</a>.
      </div>

      <div class="overlay" v-if="scored">
        <div class="row">
          <div>
            <h4>Your Score:</h4>
            <h3 class="flex">{{ score }} out of 25</h3>
            <p>For more information, check out the <a href="https://security.googleblog.com/2015/07/new-research-comparing-how-security.html">Google Security blog post</a> that inspired this game, or <a href="https://www.us-cert.gov/ncas/tips/ST04-003">this article</a> from the United States Computer Emergency Readiness Team (US-CERT)'s <a href="https://www.us-cert.gov/ncas/tips">tips list</a>. You can also click on any of the advice in the <em>Security Experts' Top Online Safety Practices</em> chart to learn more about that topic.</p>
          </div>
          <div>
            <h3 class="flex">Security Experts' Top Online Safety Practices</h3>
            <ul class="list-group">
              <li class="list-group-item" v-for="item in experts" :key="item.order">
                <img :src="'images/' + item.order + '.jpg'" />
                <div><a :href="item.link">{{ item.name }}</a></div>
              </li>
            </ul>
          </div>
        </div>
        <div class="buttons flex">
          <button type="button" @click="reset">Play Again</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";

// https://security.googleblog.com/2015/07/new-research-comparing-how-security.html
const correctAnswers = [
  { name: "Install software updates", link: "https://www.us-cert.gov/ncas/tips/ST04-006" },
  { name: "Use unique passwords", link: "https://haveibeenpwned.com/Passwords" },
  { name: "Use two-factor authentication", link: "https://twofactorauth.org/" },
  { name: "Use strong passwords", link: "https://explainxkcd.com/wiki/index.php/936:_Password_Strength" },
  { name: "Use a password manager", link: "https://www.theverge.com/2017/7/24/15921282/best-password-manager-1password-lastpass-dashlane-how-to" },
];

const incorrectAnswers = [
  "Use antivirus software",
  "Change passwords frequently",
  "Only visit websites you know",
  "Don't share personal information",
];

const experts = correctAnswers.map((item, i) => ({
  name: item.name,
  order: i + 1,
  link: item.link,
}));

function shuffle(array) {
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.random() * (i + 1) | 0;
    [array[i], array[j]] = [array[j], array[i]];
  }
}

function buildChoices() {
  const all = [
    ...correctAnswers.map((item, i) => ({ name: item.name, order: i + 1 })),
    ...incorrectAnswers.map((name, i) => ({ name, order: correctAnswers.length + i + 1 })),
  ];
  shuffle(all);
  return all;
}

export default {
  name: "SecuritySquabble",
  components: { draggable },
  data() {
    return {
      choices: buildChoices(),
      answers: [],
      scored: false,
      score: 0,
    };
  },
  methods: {
    submitScore() {
      this.score = this.answers.reduce((total, item, i) => {
        if (item.order <= correctAnswers.length) {
          return total + 5 - Math.abs(item.order - (i + 1));
        }
        return total;
      }, 0);
      this.scored = true;
    },
    reset() {
      this.score = 0;
      this.scored = false;
      this.choices = buildChoices();
      this.answers = [];
    },
  },
  computed: {
    scoreable() {
      return !this.scored && this.answers.length === 5;
    },
    experts() {
      return experts;
    },
  },
};
</script>

<style>
body, h3, h4, p, ul, li {
  margin: 0;
  padding: 0;
}

body {
  color: #111;
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
  display: flex;
  align-items: center;
  background: #eee;
  border: 2px solid black;
  cursor: move;
  margin: -2px;
  text-transform: uppercase;
}

li:hover {
  background: #fff;
}

li > div {
  display: flex;
  align-items: center;
  height: 3rem;
  padding: 0 1rem;
}

li a, li a:hover, li a:visited {
  color: #111;
  text-decoration: none;
}

li > img {
  height: 3rem;
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

.overlay {
  background: rgba(238, 238, 238, 0.95);
  height: 100%;
  left: 0;
  position: absolute;
  width: 100%;
  top: 0;
  z-index: 1000;
}

.overlay > div {
  margin: 0 auto;
  max-width: 64rem;
}

.row {
  display: flex;
  flex-wrap: wrap;
  margin: 0 auto;
  padding: 0.5rem;
}

.row > div {
  flex: 1 1 100%;
  margin: 0.5rem;
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
    flex: 1 1 calc(50% - 1rem);
  }
}
</style>

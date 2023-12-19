<template>
  <div class="section">
    <div class="container block">
      <div class="columns block">
        <div class="column is-four-fifths has-background-danger">
          <div class="container flex">
            <div
              class="card trader-card"
              :class="{
                'card-active': viewPrapor,
                'has-background-grey': !viewPrapor,
              }"
              @click="viewPrapor = !viewPrapor"
            >
              <div class="card-content">
                <div class="content">Prapor</div>
              </div>
            </div>
            <div
              class="card trader-card"
              :class="{
                'card-active': viewTherapist,
                'has-background-grey': !viewTherapist,
              }"
              @click="viewTherapist = !viewTherapist"
            >
              <div class="card-content">
                <div class="content">Therapist</div>
              </div>
            </div>
            <div
              class="card trader-card"
              :class="{
                'card-active': viewFence,
                'has-background-grey': !viewFence,
              }"
              @click="viewFence = !viewFence"
            >
              <div class="card-content">
                <div class="content">Fence</div>
              </div>
            </div>
            <div
              class="card trader-card"
              :class="{
                'card-active': viewSkier,
                'has-background-grey': !viewSkier,
              }"
              @click="viewSkier = !viewSkier"
            >
              <div class="card-content">
                <div class="content">Skier</div>
              </div>
            </div>
          </div>
          <div class="container flex">
            <div
              class="card trader-card"
              :class="{
                'card-active': viewPeacekeeper,
                'has-background-grey': !viewPeacekeeper,
              }"
              @click="viewPeacekeeper = !viewPeacekeeper"
            >
              <div class="card-content">
                <div class="content">Peacekeeper</div>
              </div>
            </div>
            <div
              class="card trader-card"
              :class="{
                'card-active': viewMechanic,
                'has-background-grey': !viewMechanic,
              }"
              @click="viewMechanic = !viewMechanic"
            >
              <div class="card-content">
                <div class="content">Mechanic</div>
              </div>
            </div>
            <div
              class="card trader-card"
              :class="{
                'card-active': viewRagman,
                'has-background-grey': !viewRagman,
              }"
              @click="viewRagman = !viewRagman"
            >
              <div class="card-content">
                <div class="content">Ragman</div>
              </div>
            </div>
            <div
              class="card trader-card"
              :class="{
                'card-active': viewJaeger,
                'has-background-grey': !viewJaeger,
              }"
              @click="viewJaeger = !viewJaeger"
            >
              <div class="card-content">
                <div class="content">Jaeger</div>
              </div>
            </div>
            <div class="search-form is-one-third">
              <div>
                <input
                  v-model="searchText"
                  class="input"
                  type="text"
                  placeholder="タスク検索"
                />
              </div>
            </div>
          </div>
        </div>
        <div class="column side-menu has-background-warning">
          <button
            v-if="!autoComplete"
            class="level-item button is-info is-light"
            @click="toggleAutoComplete"
          >
            前提タスクを自動完了：今OFF
          </button>
          <button
            v-else
            class="level-item button is-info"
            @click="toggleAutoComplete"
          >
          前提タスクを自動完了：今ON
          </button>
          <button
            v-if="!displayDoneTasks"
            class="level-item button is-info is-light"
            @click="toggleDisplayDoneTask"
          >
            完了済を表示
          </button>
          <button
            v-else
            class="level-item button is-info"
            @click="toggleDisplayDoneTask"
          >
            完了済を隠す
          </button>
          <button
            class="level-item button is-danger"
            @click="resetExecTask"
          >
            進捗をリセットする
          </button>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="columns">
        <div class="column is-four-fifths task-list has-background-link">
          <template v-for="task in filteredTasks" :key="task.id">
            <div class="box">
              <div class="card-name-line">
                {{ task.name }}
              </div>
              <div class="flex">
                <div class="card-info-line">
                  <div class="card-link">
                    <a
                      :href="generateWikiUrl(task)"
                      target="_blank"
                      rel="noopener noreferrer"
                      class="has-text-link"
                    >
                      日本語wiki
                      <span class="material-symbols-outlined google-icon">
                        open_in_new
                      </span>
                    </a>
                  </div>
                  <div class="card-link">
                    <a
                      :href="task.wikiLink"
                      target="_blank"
                      rel="noopener noreferrer"
                      class="has-text-link"
                    >
                      英語wiki
                      <span class="material-symbols-outlined google-icon">
                        open_in_new
                      </span>
                    </a>
                  </div>
                </div>
                <div class="done-button">
                  <button
                    v-if="execList.includes(task.id)"
                    class="button is-info is-light"
                    @click="toggleDoneState(task.id)"
                  >
                    UNDONE
                  </button>
                  <button
                    v-else
                    class="button is-primary"
                    @click="toggleDoneState(task.id)"
                  >
                    DONE!
                  </button>
                </div>
              </div>
            </div>
          </template>
        </div>
        <div
          class="column side-menu has-background-warning"
          @click="changeProgressView()"
        >
          <h3 class="title is-3">進捗率</h3>
          <div style="transform: rotate(-90deg)">
            <svg
              height="100%"
              viewBox="0 0 20 20"
              width="100%"
              style="overflow: visible"
            >
              <circle
                cx="50%"
                cy="50%"
                fill="none"
                stroke-width="2"
                r="9"
                stroke="#E6ECF0"
              />
              <circle
                cx="50%"
                cy="50%"
                fill="none"
                stroke-width="2"
                r="9"
                stroke="#1DA1F2"
                style="stroke-dasharray: 56.5487"
                :style="{ 'stroke-dashoffset': 56.5487 - progress * 0.565487 }"
              />
            </svg>
          </div>
          <p v-if="progressViewMode === 1" class="title is-1 progress-count">
            {{ progress }}%
          </p>
          <p
            v-else-if="progressViewMode === 2"
            class="title is-1 progress-count"
          >
            {{ execList.length }}/{{ kappaRequireTasks.length }}
          </p>
          <a
            target="_blank"
            href="https://twitter.com/share?ref_src=twsrc%5Etfw"
            class="twitter-share-button"
            data-size="large"
            :data-text="tweetText"
            data-url="https://tonamichan.github.io/kappa_chan/"
            data-hashtags="トナちゃんツール"
            data-dnt="true"
            data-show-count="false"
            >進捗を共有する</a
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script>
module.exports = {
  data() {
    return {
      tasks: [],
      execList: [],
      displayDoneTasks: false,
      viewPrapor: true,
      viewTherapist: true,
      viewFence: true,
      viewSkier: true,
      viewPeacekeeper: true,
      viewMechanic: true,
      viewRagman: true,
      viewJaeger: true,
      viewLightkeeper: true,
      progressViewMode: 1,
      searchText: "",
      autoComplete: false
    };
  },
  mounted() {
    if (localStorage.execList) {
      this.execList = JSON.parse(localStorage.execList);
    }
    this.setTaskData()
  },
  computed: {
    kappaRequireTasks: function () {
      tasks = this.tasks.filter((task) => task.kappaRequired);
      return tasks;
    },
    filteredTasks: function () {
      if (this.searchText === "") {
        return this.displayTasks;
      }

      return this.displayTasks.filter((task) =>
        task.name.toLowerCase().includes(this.searchText)
      );
    },
    displayTasks: function () {
      // 計算量注意
      tasks = this.kappaRequireTasks.filter((task) => {
        return (
          (this.viewPrapor || task.trader.name !== "Prapor") &&
          (this.viewTherapist || task.trader.name !== "Therapist") &&
          (this.viewFence || task.trader.name !== "Fence") &&
          (this.viewSkier || task.trader.name !== "Skier") &&
          (this.viewPeacekeeper || task.trader.name !== "Peacekeeper") &&
          (this.viewMechanic || task.trader.name !== "Mechanic") &&
          (this.viewRagman || task.trader.name !== "Ragman") &&
          (this.viewJaeger || task.trader.name !== "Jaeger") &&
          (this.displayDoneTasks || !this.execList.includes(task.id))
        );
      });
      return tasks;
    },
    progress: function () {
      per = this.execList.length / this.kappaRequireTasks.length;
      return isNaN(per) ? 0 : Math.floor(100 * per);
    },
    tweetText: function () {
      return `うおお！Kappa必須タスクが${this.progress}%終わったよ！`;
    },
  },
  methods: {
    setTaskData: function() {
      fetch("./task-data/task.json")
      .then((response) => response.json())
      .then((json) => this.tasks = json.data);
    },
    toggleDoneState: function (id) {
      const index = this.execList.findIndex((el) => el == id);
      if (index === -1) {
        // 新規に完了した
        this.execList.push(id);
        const doneTask = this.kappaRequireTasks.find((el) => el.id == id);
        if (doneTask.taskRequirements.length > 0) {
          // 前提タスクがあり、終わってなかった場合、DONEを付ける
          doneTask.taskRequirements.forEach(requireInfo => {
            // 受注しただけで発生するタスクとかは省きたい
            if (requireInfo.status.length != 1 || requireInfo.status[0] != "complete") { return }

            const requireTaskIndex = this.execList.findIndex((el) => el == requireInfo.task.id);
            if (requireTaskIndex === -1) {
              // 前提タスクが完了マークされてないので、マークする
              this.toggleDoneState(requireInfo.task.id)
            }
          })
        }
      } else {
        // 完了から戻した
        this.execList.splice(index, 1);
      }

      localStorage.execList = JSON.stringify(this.execList, undefined, 1);
    },
    toggleDisplayDoneTask: function () {
      this.displayDoneTasks = !this.displayDoneTasks;
    },
    toggleAutoComplete: function () {
      this.autoComplete = !this.autoComplete;
    },
    resetExecTask: function () {
      // TODO: 確認メッセージ出す
      this.execList = []
      localStorage.execList = JSON.stringify(this.execList, undefined, 1);
    },
    generateWikiUrl: function (task) {
      // 末尾のハテナのはほぼWhat’s on the Flash Drive?用
      return (
        "https://wikiwiki.jp/eft/" +
        task.trader.name +
        "/" +
        task.name.replace(/[?]/g, "%3F")
      );
    },
    changeProgressView: function() {
      this.progressViewMode = this.progressViewMode + 1
      if (this.progressViewMode === 3) { this.progressViewMode = 1 }
    }
  },
};
</script>

<style scoped>
.done-button {
  margin-left: auto;
}
.side-menu {
  text-align: center;
}
.card-info-line {
  margin-right: auto;
  padding-top: 1rem;
  display: flex;
}
.card-name-line {
  font-weight: 600;
  display: flex;
}
.flex {
  display: flex;
}
.google-icon {
  vertical-align: -6px;
}
.card-content {
  width: 8rem;
}
.trader-card {
  margin: 2px;
  cursor: pointer;
}
.box {
  margin-bottom: 0.5rem;
}
.icon-on-card {
  margin-left: 2px;
}
.progress-count {
  position: relative;
  bottom: 160px;
}
.card-link {
  margin-right: 1rem;
}
.search-form {
  margin-left: auto;
  margin-top: auto;
  padding: 2px;
}
</style>

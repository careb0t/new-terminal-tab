<template>
  <div class="main-container">
    <div class="terminal">
      <div class="title-bar">
        <span>{{ username }}@{{ hostname }}</span>
      </div>
      <div class="commands">
        <div v-for="(command, index) in commands" class="command">
          <div class="command-header">
            <div class="left-group">
              <div class="user">
                <div class="user-root">{{ username }}@{{ hostname }}</div>
                <div class="user-ender"></div>
              </div>
              <div class="path">
                <div class="path-root">~</div>
                <div class="path-ender"></div>
              </div>
            </div>
            <div class="right-group">
              <div class="execute">
                <div class="execute-ender"></div>
                <div class="execute-root">
                  {{ didExecute(command.execute) }}
                </div>
              </div>
              <div class="time">
                <div class="time-ender"></div>
                <div class="time-root">{{ command.timestamp }}</div>
              </div>
            </div>
            <div class="connectors"></div>
          </div>
          <div class="input-container">
            <input
              class="input"
              ref="inputs"
              type="text"
              v-model="command.input"
              v-on:keyup.enter="submitCommand(command.input, index)"
              :disabled="!isLatestCommand(index)"
            />
          </div>
          <div v-if="Array.isArray(command.response)" class="response">
            <ul>
              <li v-for="(item, idx) in command.response" :key="idx">
                {{ item }}
              </li>
            </ul>
          </div>
          <div v-else-if="command.response" class="response">
            {{ command.response }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watchEffect, h } from "vue";

let initialTime = getTimestamp();
let initialCommand = {
  response: null,
  timestamp: initialTime,
  execute: true,
  input: null,
};

const username = ref("careb0t");
const hostname = ref("careb0t-desktop");
const commands = ref([initialCommand]);
const inputs = ref([]);

watchEffect(() => {
  if (inputs.value.length === 0) return;
  inputs.value.at(-1)?.focus();
});

function getTimestamp() {
  let today = new Date();
  let hours = today.getHours();
  let minutes = today.getMinutes();
  let seconds = today.getSeconds();
  let amOrPm = hours <= 12 ? "AM" : "PM";
  hours = hours % 12 || 12;
  minutes = minutes < 10 ? `0${minutes}` : minutes;
  seconds = seconds < 10 ? `0${seconds}` : seconds;
  let time = `${hours}:${minutes}:${seconds} ${amOrPm}`;
  return time;
}

function submitCommand(input, index) {
  let newCommand = {
    response: null,
    timestamp: getTimestamp(),
    execute: true,
    input: null,
  };

  if (input === "help") {
    let commandList = [
      "help - display a list of available commands",
      "clear - clear the terminal screen",
      "search [query] - search the web",
    ];
    commands.value[index].response = commandList;
  } else if (input === "clear") {
    commands.value = [];
  } else if (input.startsWith("search")) {
    let args = input.split(" ").slice(1).join(" ");
    window.location.href = `https://www.google.com/search?q=${args}`;
  } else {
    commands.value[index].response = `Command not found: ${input}`;
    newCommand.execute = false;
  }

  commands.value.push(newCommand);
}

function isLatestCommand(index) {
  return index === commands.value.length - 1;
}

function didExecute(execute) {
  return execute ? "✔" : "✗";
}
</script>

<style scoped>
.main-container {
  padding: 100px;

  .terminal {
    height: 50vh;
    width: 40vw;
    display: flex;
    flex-direction: column;

    .title-bar {
      background-color: #2b313c;
      opacity: 1;
      height: 35px;
      font-family: "Ubuntu", sans-serif;
      font-weight: 700;
      color: #898d9b;
      font-size: 14px;
      text-align: center;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .commands {
      background-color: #2e3440;
      opacity: 0.9;
      height: auto;
      flex-grow: 1;

      .command-header {
        position: relative;
        display: flex;
        flex-direction: row;
        align-items: center;
        padding: 10px 30px 5px 30px;
        height: 24px;
        justify-content: space-between;

        .left-group {
          display: flex;

          .user {
            display: flex;
            flex-direction: row;
            z-index: 2;
            height: 100%;
            .user-root {
              background-color: #e5e9f0;
              padding: 2px 10px;
              font-size: 14px;
              color: #2e3440;
            }
            .user-ender {
              width: 0;
              height: 0;
              border-style: solid;
              border-width: 12px 0 12px 10px;
              border-color: transparent transparent transparent #e5e9f0;
            }
          }
          .path {
            display: flex;
            flex-direction: row;
            z-index: 1;
            height: 100%;
            margin-left: -10px;
            .path-root {
              background-color: #81a1c1;
              padding: 2px 10px;
              padding-left: 20px;
              font-size: 14px;
              color: #2e3440;
            }
            .path-ender {
              width: 0;
              height: 0;
              border-style: solid;
              border-width: 12px 0 12px 10px;
              border-color: transparent transparent transparent #81a1c1;
            }
          }
        }

        .right-group {
          display: flex;

          .execute {
            display: flex;
            flex-direction: row;
            z-index: 1;
            height: 100%;
            margin-right: -10px;
            .execute-ender {
              width: 0;
              height: 0;
              border-style: solid;
              border-width: 12px 10px 12px 0;
              border-color: transparent #3b4252 transparent transparent;
            }
            .execute-root {
              background-color: #3b4252;
              padding: 2px 10px;
              padding-right: 20px;
              font-size: 14px;
              color: #a3be8c;
            }
          }
          .time {
            display: flex;
            flex-direction: row;
            z-index: 2;
            height: 100%;
            .time-ender {
              width: 0;
              height: 0;
              border-style: solid;
              border-width: 12px 10px 12px 0;
              border-color: transparent #e5e9f0 transparent transparent;
            }
            .time-root {
              background-color: #e5e9f0;
              padding: 2px 10px;
              font-size: 14px;
              color: #2e3440;
            }
          }
        }

        .connectors {
          position: absolute;
          --l: 12px; /* the amount of left position shift */
          left: var(--l);
          top: 22px;
          width: calc(100% - var(--b) - var(--s) - (var(--l) / 2) - 1px);
          height: 21px;
          --s: 18px; /* the border's visible horizontal length */
          --b: 3px; /* the width of the border */
          border: var(--b) solid #474747; /* the thickness and color */
          border-radius: 4px;
          mask: conic-gradient(at var(--s) var(--s), #0000 25%, #000 0) 0 0 /
              calc(100% - var(--s)) calc(100% - var(--s)),
            linear-gradient(#000 0 0) content-box;
        }
      }

      .input-container {
        padding: 0 38px;

        .input {
          background-color: transparent;
          border: none;

          font-weight: 400;
          font-size: 16px;
          color: #d6b5d6;
        }

        .input:focus {
          outline: none;
        }
      }

      .response {
        font-weight: 400;
        font-size: 16px;
        color: #dfe3ed;

        ul {
          list-style-type: none;
          padding: 0;
        }

        li {
          padding-left: 10px;
        }
      }
    }
  }
}
</style>

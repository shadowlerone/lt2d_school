<template>
  <div>
    <div v-if="$route.params" id="content">
      <div id="header">
        <span class="connState"></span>
        <!--<button id="start-togetherjs" type="button"
          onclick="TogetherJS(this); return false"
          data-end-togetherjs-html="End TogetherJS">
          Start TogetherJS
        </button>-->
        <div id="save"></div>

        <div id="logo">
          <img
            src="assets/Preville-Logo-white.svg"
            alt="Preville Logo"
            style="height: 37px; padding: 6px 6px 6px 6px; margin-left: 60px"
            id="logoImg"
          />
        </div>
        <h1>{{ thisRooms.name }}</h1>

        <div class="modal-container">
          <input
            id="modal-toggle"
            type="checkbox"
            v-model="thisRooms.checked"
          />
          <button>Onload Info</button>
          <div class="modal-backdrop">
            <div class="modal-content">
              <label class="modal-close" for="modal-toggle">x</label>
              <h1>{{ thisRooms.name }}</h1>
              <div>
                <h2>{{ thisRooms.alt }}</h2>
                <h2>{{ thisRooms.meet }}</h2>
                <!--<h3>{{ $route.params }}</h3>-->
              </div>
              <label class="modal-close button" for="modal-toggle">Close</label>
            </div>
          </div>
        </div>
        <div style="display:none">
          <input type="radio" name="app_state" value="half" checked="checked" />
          <input type="radio" name="app_state" value="app_fullscreen" />
          <input type="radio" name="app_state" value="meeting_fullscreen" />
        </div>
        <div id="open" style="right: 1%; position: fixed">
          <a>
            <svg
              id="app_opener"
              width="33"
              height="22"
              viewBox="0 0 33 22"
              fill="none"
              xmlns="http://www.w3.org/2000/svg"
              class="noselect half"
              onclick="openWindowSide()"
            >
              <rect
                id="back-fill"
                x="1"
                y="1"
                width="13"
                height="20"
                rx="5"
                fill="white"
                stroke="white"
                stroke-width="2"
                opacity="0.5"
              />

              <!-- <path d="M0 6C0 2.68629 2.68629 0 6 0H14V22H6C2.68629 22 0 19.3137 0 16V6Z" fill="green" fill-opacity="1" /> -->
              <rect
                x="1"
                y="1"
                width="31"
                height="20"
                rx="5"
                stroke="white"
                stroke-width="2"
              />
              <path
                id="right-arrow"
                d="M27.3536 11.3536C27.5488 11.1583 27.5488 10.8417 27.3536 10.6464L24.1716 7.46447C23.9763 7.2692 23.6597 7.2692 23.4645 7.46447C23.2692 7.65973 23.2692 7.97631 23.4645 8.17157L26.2929 11L23.4645 13.8284C23.2692 14.0237 23.2692 14.3403 23.4645 14.5355C23.6597 14.7308 23.9763 14.7308 24.1716 14.5355L27.3536 11.3536ZM19 11.5L27 11.5V10.5L19 10.5V11.5Z"
                fill="white"
                stroke="white"
              />
              <path id="v-line" d="M14 0V22" stroke="white" stroke-width="2" />
            </svg>
          </a>
          <a
            :href="'https://meet.jit.si/' + thisRooms.meet"
            target="meeting_iframe"
            class="dot"
            style="background-color: #ff5555"
            id="dot1"
            onclick="breakout(this)"
          >
            <img src="assets/home.svg" />
          </a>
          <a
            v-for="n in thisRooms.classroom.breakout_rooms"
            v-bind:key="n"
            :href="
              'https://meet.jit.si/' +
                thisRooms.meet +
                '-breakout-room-' +
                n +
                config.meetingSettings
            "
            class="dot"
            target="meeting_iframe"
            onclick="breakout(this)"
          >
            <span>{{ n }}</span>
          </a>
          <a
            class="dot support"
            target="_blank"
            onclick=""
            href="https://www.centrepreville.org/camp-f-a-q-troubleshooting"
          >
            <span>?</span>
          </a>
        </div>
      </div>
      <div id="myModal" class="modal">
        <!-- Modal content -->
        <div class="modal-content">
          <p class="rotate">Please rotate your device.</p>
        </div>
      </div>
      <div class="window">
        <iframe
          allow="microphone; camera"
          style="width: 0%; height: calc(100% - 50px); border: none;"
          src="https://www.centrepreville.org/camp-f-a-q-troubleshooting"
          id="faq"
          name="faq_iframe"
        ></iframe>
        <template
          v-if="thisRooms.classroom.app_set[0].name == 'Shared Whiteboard'"
        >
          <iframe
            allow="microphone; camera"
            :src="
              thisRooms.classroom.app_set[0].iframe +
                thisRooms.name.split('/')[0]
            "
            id="apps"
            name="app_iframe"
            class="Close"
          ></iframe>
        </template>
        <template v-else>
          <iframe
            allow="microphone; camera"
            :src="thisRooms.classroom.app_set[0].iframe"
            id="apps"
            name="app_iframe"
            class="Close"
          ></iframe>
        </template>
        <iframe
          allow="microphone; camera"
          :src="
            'https://meet.jit.si/' + thisRooms.meet + config.meetingSettings
          "
          id="jitsi"
          name="meeting_iframe"
          class="Open"
        >
          <p>Your browser does not support iframes.</p>
        </iframe>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "meet",
  title: "Préville - Preville",
  created: function() {
    //Populate empty meeting
  },
  data: function() {
    return {
      app: "",
      config: {
        meetingSettings:
          "#jitsi_meet_external_api_id=0&amp;config.requireDisplayName=true&amp;config.startAudioMuted=6&amp;config.disableAudioLevels=true&amp;interfaceConfig.DISABLE_VIDEO_BACKGROUND=true&interfaceConfig.SHOW_CHROME_EXTENSION_BANNER=false&config.disableDeepLinking=true&setVideoQuality=720",
      },
    };
  },
  computed: {
    params: function() {
      if (!this.$route.params.meet) {
        return {
          meet: "Foyer de Préville - Preville Lobby",
          alt: "Foyer de Préville - Preville Lobby",
          name: "Foyer de Préville - Preville Lobby",
          title: "Hall",
          frameposition: 0,
          breakout_rooms: 1,
        };
      } else {
        return this.$route.params;
      }
    },
    app_set: function() {
      if (!this.$route.params.app_set) {
        return [
          {
            name: "FAQ",
            logo:
              "https://centrepreville.org/wp-content/uploads/2020/12/Pre%CC%81villeLogoNEW-300x153.png",
            iframe: "https://centrepreville.org/camp-f-a-q-troubleshooting/",
          },
        ];
      } else {
        return this.$route.params.app_set;
      }
    },
    meetingRooms: function() {
      return parseInt(this.params.breakout_rooms);
    },
    thisRooms: function() {
      return this.$parent.rooms.find(
        (room) => room.meet == this.$route.params.meet
      );
    },
  },
};
</script>
<style scoped>
a {
  color: #444499;
  text-decoration: none;
}

a:hover {
  color: #333;
}

#header {
  position: fixed;
  top: 0;
  left: 0px;
  height: 50px;
  width: 100%;
  background: #212533;
  padding: 0;
  z-index: 10000;
}

#header img {
  max-width: 80%;
}

#apps {
  background-color: white;
}

#logo {
  display: inline;
  position: absolute;
  left: 0px;
  background: #212533;
  height: 50px;
}

h1 {
  margin-top: 5px;
  padding-top: 5px;
  color: #f1f1f1;
  font-size: 1.6em;
}

.modal-container {
  margin: 0 auto;
  padding-top: 10px;
  position: relative;
  width: 160px;
}

.modal-container button {
  display: none;
  margin: 0 auto;
  color: #fff;
  width: 160px;
  height: 50px;
  line-height: 50px;
  background: #446cb3;
  font-size: 22px;
  border: 0;
  border-radius: 3px;
  box-shadow: 0 5px 5px -5px #333;
  transition: background 0.3s ease-in;
}

.modal-container .modal-backdrop {
  height: 0;
  width: 0;
  opacity: 0;
  overflow: hidden;
  transition: opacity 0.2s ease-in;
}

.modal-container #modal-toggle {
  position: absolute;
  left: 0;
  top: 0;
  height: 100%;
  width: 100%;
  margin: 0;
  opacity: 0;
  cursor: pointer;
}

.modal-container #modal-toggle:hover ~ button {
  background: #1e824c;
}

.modal-container #modal-toggle:checked {
  width: 100vw;
  height: 100vh;
  position: fixed;
  left: 0;
  top: 0;
  z-index: 9;
  opacity: 0;
}

.modal-container #modal-toggle:checked ~ .modal-backdrop {
  background-color: rgba(0, 0, 0, 0.6);
  width: 100vw;
  height: 100vh;
  position: fixed;
  left: 0;
  top: 0;
  z-index: 9;
  pointer-events: none;
  opacity: 1;
}

.modal-container #modal-toggle:checked ~ .modal-backdrop .modal-content {
  background-color: #fff;
  max-width: 400px;
  width: 100%;
  height: 280px;
  padding: 10px 30px;
  position: absolute;
  left: calc(50% - 200px);
  top: 12%;
  border-radius: 4px;
  z-index: 999;
  pointer-events: auto;
  cursor: auto;
  box-shadow: 0 3px 7px rgba(0, 0, 0, 0.6);
}
@media (max-width: 400px) {
  .modal-container #modal-toggle:checked ~ .modal-backdrop .modal-content {
    left: 0;
  }
}

.modal-container
  #modal-toggle:checked
  ~ .modal-backdrop
  .modal-content
  .modal-close {
  color: #666;
  position: absolute;
  right: 2px;
  top: 0;
  padding-top: 7px;
  background: #fff;
  font-size: 16px;
  width: 25px;
  height: 28px;
  font-weight: bold;
  text-align: center;
  cursor: pointer;
}

.modal-container
  #modal-toggle:checked
  ~ .modal-backdrop
  .modal-content
  .modal-close.button {
  top: initial;
  bottom: 20px;
  right: 20px;
  background: #4caf50;
  color: #fff;
  width: 50px;
  border-radius: 2px;
  font-size: 14px;
  font-weight: normal;
}

.modal-container
  #modal-toggle:checked
  ~ .modal-backdrop
  .modal-content
  .modal-close.button:hover {
  color: #fff;
  background: #1e824c;
}

.modal-container
  #modal-toggle:checked
  .modal-backdrop
  .modal-content
  .modal-close:hover {
  color: #333;
}

.window {
  width: 100vw;
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  background: rgba(32, 42, 54, 1);
}

#apps,
#jitsi {
  height: calc(100vh - 45px);
  border: none;
  margin: none;
  padding: none;
  top: 50px;
  left: 0px;
}

.Open {
  width: 100vw;
}
.Half {
  width: 50vw;
}
.Close {
  width: 0vw;
}

#open {
  /* position: fixed; */
  top: 5px;
  right: 1vw;
  z-index: 99999;
  display: flex;
  background-color: #212533;
  padding-left: 0.25rem;
}

.dot {
  height: 28px;
  min-width: 28px;
  font-weight: 600;
  /* background-color: #bbb; */
  border-radius: 50%;
  /* display: inline-block; */
  margin-left: 25px;
  border: 3px solid #f1f1f1;
  text-align: center;
  color: white;
  background-color: transparent;
  cursor: pointer;
}
.dot.active {
  background-color: lime !important;
}
.dot:hover {
  background-color: #bbb !important;
}
.dot > * {
  /* vertical-align: bottom; */
  line-height: 28px;
  font-size: larger;
  font-weight: inherit;
}
.dot > img {
  height: 18px;
  /* vertical-align:baseline; */
  margin-top: 3px;
}

#app_opener {
  cursor: pointer;
  height: 36px;
}
#app_opener * {
  transition: width 0.3s ease-in, d 0.3s ease-in, transform 0.3s ease-in, stroke-width 0.3s ease-in ;
}
#app_opener.half:hover #v-line, #app_opener:hover #right-arrow {
  animation-iteration-count: 1;
  animation-duration: 0.50s;
  animation-name: app_opener_hover;
  animation-timing-function: linear;
}
#app_opener.meeting_fullscreen:hover #right-arrow {
  animation-iteration-count: 1;
  animation-duration: 0.50s;
  animation-name: app_opener_hover;
  animation-timing-function: linear;
  animation-direction: reverse;
}
#app_opener.app_fullscreen #back-fill {
  width: 31px;
}
#app_opener.meeting_fullscreen #back-fill {
  width: 0;
}

#app_opener.app_fullscreen #v-line {
  transform: translate(17px);
  stroke-width: 1;
}
#app_opener.meeting_fullscreen #v-line {
  transform: translate(-12px);
  stroke-width: 1;
}

@keyframes app_opener_hover {
  0% {
    transform: translate(0);
  }
  25% {
    transform: translate(1px);
  }
  50% {
    transform: translate(0px);
  }
  75% {
    transform: translate(-1px);
  }
  100% {
    transform: translate(0);
  }
}

#app_opener.meeting_fullscreen #right-arrow {
  d: path(
    "M6.64645 10.6464C6.45118 10.8417 6.45118 11.1583 6.64645 11.3536L9.82843 14.5355C10.0237 14.7308 10.3403 14.7308 10.5355 14.5355C10.7308 14.3403 10.7308 14.0237 10.5355 13.8284L7.70711 11L10.5355 8.17157C10.7308 7.97631 10.7308 7.65973 10.5355 7.46447C10.3403 7.2692 10.0237 7.2692 9.82843 7.46447L6.64645 10.6464ZM15 10.5L7 10.5L7 11.5L15 11.5L15 10.5Z"
  );
}

.noselect {
  -webkit-touch-callout: none; /* iOS Safari */
  -webkit-user-select: none; /* Safari */
  -khtml-user-select: none; /* Konqueror HTML */
  -moz-user-select: none; /* Old versions of Firefox */
  -ms-user-select: none; /* Internet Explorer/Edge */
  user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome, Edge, Opera and Firefox */
}

/* The Modal (background) */
.modal {
  display: none; /* Hidden by default */
  position: fixed; /* Stay in place */
  z-index: 1; /* Sit on top */
  padding-top: 100px; /* Location of the box */
  padding-bottom: 100px; /* Location of the box */
  left: 0;
  top: 0;
  width: 100%; /* Full width */
  height: 100%; /* Full height */
  overflow: auto; /* Enable scroll if needed */
  background-color: rgb(0, 0, 0); /* Fallback color */
  background-color: rgba(0, 0, 0, 0.4); /* Black w/ opacity */
}

/* Modal Content */
.modal-content {
  background-color: #fefefe;
  margin: auto;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
  height: 80%;
  font-size: 3rem;
}
.rotate {
  transform: rotate(-90deg) translate(-100%, 0%);
}
/*@media screen and (orientation: portrait) {
  .modal {
    display: block;
  }
}*/
</style>

<template>
  <div>
    <section v-show="!deleteLoading" id="loading">
      <div>
        <img src="/img/earth.svg" />
        <p>Veuillez patienter...</p>
      </div>
    </section>
    <section id="canvas"></section>
    <section id="front">
      <div class="container-fluid">
        <div class="row">
          <div
            id="bloc_retract"
            v-bind:class="{ translation: showInformation }"
            v-bind:style="[
              showInformation
                ? { visibility: 'visible' }
                : { visibility: 'hidden' },
            ]"
            class="col-12 col-lg-3 bloc_Blanc"
          >
            <div id="imagePrincipale">
              <img :src="`${informations.image.url}`" alt="" />
            </div>

            <div id="infos">
              <h1>{{ informations.nomDeVille }}</h1>
              <p>{{ informations.date }}</p>
              <hr />
              <p v-html="informations.descriptif"></p>
              <NuxtLink
                :to="`/destinations/${informations.slug}`"
                class="btn_stroke"
                >Voir la destination</NuxtLink
              >
              <div id="backToCamera" @click="returnToCamera">
                <p>Retour aux projets</p>
              </div>
            </div>
          </div>
          <div
            v-if="showInformation"
            v-bind:class="{ leftTranslation: showInformation }"
            v-bind:style="[
              showInformation
                ? { visibility: 'visible' }
                : { visibility: 'hidden' },
            ]"
            id="bloc_left"
            class="left"
          >
            <h2 id="titre_Projects"></h2>
            <h3 id="soustitre_Projects"></h3>
            <p id="paragraphe_Projects"></p>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import {
  CSS3DRenderer,
  CSS3DSprite,
  CSS3DObject,
} from "three/examples/jsm/renderers/CSS3DRenderer.js";

export default {
  data() {
    return {
      villes: null,
      camera: null,
      renderer: null,
      renderer1: null,
      informations: {
        image: "",
      },
      showInformation: false,
      deleteLoading: false,
      LOADING_MANAGER: null,
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    returnToCamera() {
      this.showInformation = false;
    },
    async init() {
      var RESOURCES_LOADED = false;
      const locations = [];
      try {
        const results = await axios.get(
          "https://back-italie.herokuapp.com/destinations"
        );

        for (let i = 0; i < results.data.length; i++) {
          locations.push({
            coordX: results.data[i].coordonnees_x,
            coordY: results.data[i].coordonnees_y,
            coordZ: 0,
            ville: results.data[i].nomDeLaVille,
            id: results.data[i].Identifiant,
          });
        }
      } catch (error) {
        console.log(error);
      }
      const loadingManager = new THREE.LoadingManager();

      loadingManager.onLoad = function () {
        console.log("Finito");
        RESOURCES_LOADED = true;
        this.deleteLoading = true;
        console.log(this.deleteLoading);
      };
      const loader = new THREE.TextureLoader(loadingManager);
      const height = loader.load("/three/alpha_HD.jpg");
      const texture = loader.load("/three/texture_HD.jpg");
      const alpha = loader.load("/three/alphaSquare.png");

      const scene = new THREE.Scene();
      scene.background = new THREE.Color(0xfefee2);

      const geometry = new THREE.PlaneBufferGeometry(3, 3, 64, 64);

      const material = new THREE.MeshStandardMaterial({
        color: "white",
        map: texture,
        displacementMap: height,
        displacementScale: 0.1,
        alphaMap: alpha,
        transparent: true,
      });

      const plane = new THREE.Mesh(geometry, material);
      plane.position.set(0, 0, 0);

      scene.add(plane);

      // Mesh
      for (var i = 0; i < locations.length; i++) {
        var element = document.createElement("div");

        element.id = locations[i].id;
        element.className = "essai";

        element.style.width = "5px";
        element.style.height = "5px";
        element.style.opacity = "1";
        element.style.borderRadius = "50%";
        element.style.overflow = "auto";
        element.style.padding = "1px";
        element.style.zIndex = "90";

        element.addEventListener("click", this.onClick_label, false);
        element.style.cursor = "pointer";
        element.style.backgroundImage = "url(/img/test.png)";
        element.style.backgroundSize = "5px";
        element.style.position = "relative";
        var domObject = new CSS3DObject(element);
        domObject.position.x = locations[i].coordX;
        domObject.position.y = locations[i].coordY;
        domObject.position.z = locations[i].coordZ;
        domObject.rotation.x = 1;

        scene.add(domObject);
        domObject.scale.set(0.01, 0.01, 0.01);
      }

      // Lights

      const pointLight = new THREE.PointLight(0xffffff, 1);
      pointLight.position.x = 2;
      pointLight.position.y = 3;
      pointLight.position.z = 4;
      scene.add(pointLight);

      /**
       * Sizes
       */
      const sizes = {
        width: window.innerWidth,
        height: window.innerHeight,
      };

      /**
       * Camera
       */
      // Base camera
      this.camera = new THREE.PerspectiveCamera(
        75,
        sizes.width / sizes.height,
        0.1,
        100
      );
      this.camera.position.set(0, -0.1, 1);
      this.camera.lookAt(plane);

      scene.add(this.camera);

      /** FOG */
      // scene.background = new THREE.Color( FOG_COLOR )
      //  scene.fog = new THREE.Fog( FOG_COLOR, -10, 20 )

      /**
       * Renderer
       */
      const accueil = document.getElementById("canvas");
      console.log(accueil);

      this.renderer1 = new THREE.WebGLRenderer();
      this.renderer1.setSize(sizes.width, sizes.height);
      this.renderer1.setPixelRatio(window.devicePixelRatio);
      accueil.appendChild(this.renderer1.domElement);

      // Controls
      const controls = new OrbitControls(
        this.camera,
        this.renderer1.domElement
      );
      controls.enablePan = true;
      //controls.screenSpacePanning = false
      controls.minDistance = 0.3;
      controls.maxDistance = 1.3;
      // controls.maxPolarAngle = 2;
      // controls.minPolarAngle = 2;
      // controls.maxAzimuthAngle = 0.1;
      // controls.minAzimuthAngle = -0.1;
      controls.enableRotate = false;

      controls.mouseButtons = {
        LEFT: THREE.MOUSE.PAN,
        MIDDLE: THREE.MOUSE.DOLLY,
        RIGHT: THREE.MOUSE.ROTATE,
      };

      this.renderer = new CSS3DRenderer();
      this.renderer1.setPixelRatio(window.devicePixelRatio);

      this.renderer.setSize(window.innerWidth, window.innerHeight);

      accueil.appendChild(this.renderer.domElement);
      this.renderer.domElement.id = "labelName";

      /**
       * Animate
       */

      const clock = new THREE.Clock();
      loadingManager.onProgress = function (item, loaded, total) {
        console.log(item, loaded, total);
      };

      const tick = () => {
        if (RESOURCES_LOADED == false) {
          window.requestAnimationFrame(tick);
          return;
        }
        var test12 = document.querySelector("#loading");

        test12.classList = "cache";
        this.renderer1.render(scene, this.camera);

        this.renderer.render(scene, this.camera);
        // console.log(this.camera.position)
        window.requestAnimationFrame(tick);
      };

      window.addEventListener("resize", this.onWindowResize);
      tick();
    },
    onWindowResize() {
      this.camera.aspect = window.innerWidth / window.innerHeight;
      this.camera.updateProjectionMatrix();
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      this.renderer1.setSize(window.innerWidth, window.innerHeight);
    },
    onClick_label(event) {
      this.showInformation = true;
      this.object_ID = event.currentTarget.id;
      console.log(event);
      this.fetchDataCategory(this.object_ID);
      const blockRetra = document.getElementById("bloc_retract");
      const blockRetra1 = document.getElementById("bloc_left");
      const bloc_blanc = document.querySelector(".bloc_Blanc");

      //blockRetra.style.visibility = "visible";
      // bloc_blanc.classList.add("translation");
      // blockRetra1.style.visibility = "visible";
      //blockRetra1.classList.add("left-translation");
    },
    fetchDataCategory(id) {
      axios
        .get(`https://back-italie.herokuapp.com/destinations/${id}`)
        .then((response) => {
          this.informations = {
            nomDeVille: response.data.Titre,
            date: response.data.Date,
            descriptif: response.data.Descriptif,
            slug: response.data.Identifiant,
            image: response.data.imagePrincipale,
          };
          console.log(this.informations);
        });
    },
  },
};
</script>

<style lang='scss'>
section#loading {
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
  background-color: rgb(24, 24, 24);
  width: 100%;
  height: 100vh;
  z-index: 100;

  div {
    width: 150px;
    height: 150px;

    display: block;
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    top: 0;
    bottom: 0;
    text-align: center;
    img {
      width: 100px;
      height: 100px;
      -webkit-animation: spin 4s linear infinite;
      -moz-animation: spin 4s linear infinite;
      animation: spin 4s linear infinite;
    }
    p {
      color: white;
      margin-top: 1rem;
    }
  }
}
.cache {
  opacity:1;
  animation: 1s fadeIn;
  animation-fill-mode: forwards;

  
}
@-moz-keyframes spin {
  100% {
    -moz-transform: rotate(360deg);
  }
}
@-webkit-keyframes spin {
  100% {
    -webkit-transform: rotate(360deg);
  }
}
@keyframes spin {
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
@keyframes fadeIn {
  0%{
     opacity: 1;
  }
  99% {
    visibility: visible;
  }
  100% {
    visibility: hidden;
     opacity: 0;
  }
}
.home-links a {
  margin-right: 1rem;
}
canvas {
  position: absolute;
  top: 0;
}
#labelName {
  pointer-events: none;
  position: absolute;
  top: 0;
  .essai {
  }
}
section#canvas {
}
.bloc_Blanc {
  visibility: hidden;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 100;
  height: 100vh;
  z-index: 100;
  background: linear-gradient(to right, #fff, #fff);
  background-repeat: no-repeat;
  background-size: 0 100%;
  transition: background-size 0.6s 0s;

  img {
    width: 100%;
  }
}

.left {
  width: 42%;
  z-index: 101;
  visibility: hidden;
}
section#front {
  width: 100%;
  position: absolute;
  top: 0;
  z-index: 1000;
  pointer-events: none;
  height: 100vh;
}

.translation {
  background-size: 100% 100%;
  pointer-events: initial;
}

#bloc_retract {
  position: relative;
  padding: 0;
  #imagePrincipale {
    height: 30vh;
    img {
      object-fit: cover;
      height: 100%;
    }
  }
  #infos {
    padding-left: 3rem;
    padding-right: 2rem;
    padding-top: 2rem;
    height: 70vh;
    overflow-y: scroll;
    h1 {
      font-size: 36px;
      line-height: 121.4%;
      /* identical to box height, or 44px */

      color: #c69761;
    }
    p {
      font-family: Montserrat;
      font-style: normal;
      font-weight: 500;
      font-size: 15px;
      line-height: 121.4%;
      /* or 18px */

      color: #000000;
    }
    .btn_stroke {
      margin-top: 1rem;
      font-family: Montserrat;
      font-style: normal;
      font-weight: normal;
      font-size: 15px;
      line-height: 121.4%;
      /* or 18px */
      text-decoration: none;

      border: 1px solid #c69761;
      padding: 0.5rem 1rem;
      box-sizing: border-box;
      color: #c69761;
    }
    #backToCamera {
      margin-top: 2rem;
      cursor: pointer;
    }
  }
}
#bloc_retract:after {
  content: "";

  position: absolute;

  //background-image: url(/paper-edge.png);
  background-position: right;
  background-repeat: no-repeat;

  width: 32px;
  height: 10px;
  right: -32px;
  height: 100vh;
  top: 0;
}
</style>

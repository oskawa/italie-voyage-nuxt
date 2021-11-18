<template>
  <div>
    <section id="canvas"></section>
    <section id="front">
      <div class="container-fluid">
        <div class="row">
          <div id="bloc_retract" class="col-12 col-lg-4 bloc_Blanc">
            <h1>{{informations.nomDeVille}}</h1>
            <p>{{informations.Date}}</p>
            <NuxtLink :to="`/destinations/${informations.slug}`">Voir la destination</NuxtLink>
          </div>
          <div id="bloc_left" class="left">
            <h2 id="titre_Projects"></h2>
            <h3 id="soustitre_Projects"></h3>
            <p id="paragraphe_Projects"></p>
          </div>
        </div>
      </div>
      <div id="backToCamera">
        <p>Retour aux projets</p>
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
      camera:null,
      renderer:null,
      renderer1:null,
      informations:{}
      
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    
    async init() {
      const locations=[]
      try {
        const results = await axios.get("http://localhost:1337/destinations");
        
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

      const loader = new THREE.TextureLoader();
      const height = loader.load("/three/alpha_HD.jpg");
      const texture = loader.load("/three/texture_HD.jpg");
      const alpha = loader.load("/three/alphaSquare.png");

      const scene = new THREE.Scene();

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

      scene.add(plane);

      // Mesh
      for (var i = 0; i < locations.length; i++) {
        var element = document.createElement("div");
        element.id = locations[i].id;

        element.style.width = "2px";
        element.style.height = "2px";
        element.style.opacity = "1";
        element.style.borderRadius = "50%";

        element.style.overflow = "auto";
        element.style.background = "red";
        element.style.padding = "1px";

        element.style.zIndex = "90";
        element.style.opacity = 0.99;
        element.addEventListener("click", this.onClick_label, false);
        element.style.cursor = "pointer";

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
      this.camera.position.set(600, 400, 1500);
      this.camera.lookAt(0, 0, 0);
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
       this.renderer1.setPixelRatio(Math.min(window.devicePixelRatio, 2));
      accueil.appendChild( this.renderer1.domElement);

      // Controls
      const controls = new OrbitControls(this.camera, this.renderer1.domElement);
      controls.enableDamping = true;
      controls.keys = {
        LEFT: "ArrowLeft", //left arrow
        UP: "ArrowUp", // up arrow
        RIGHT: "ArrowRight", // right arrow
        BOTTOM: "ArrowDown", // down arrow
      };
      controls.minDistance = 0.3;
      controls.maxDistance = 2;
      controls.maxPolarAngle = 2.8;
      controls.minPolarAngle = 2;
      controls.maxAzimuthAngle = 0.1;
      controls.minAzimuthAngle = -0.1;

      this.renderer = new CSS3DRenderer();
      this.renderer.setSize(window.innerWidth, window.innerHeight);

      accueil.appendChild(this.renderer.domElement);
      this.renderer.domElement.id = "labelName";

      /**
       * Animate
       */

      const clock = new THREE.Clock();

      const tick = () => {
        const elapsedTime = clock.getElapsedTime();
        const time = performance.now();
        // Update objects

        // Update Orbital Controls
        //controls.update()

        // Render
         this.renderer1.render(scene, this.camera);
         this.renderer.render(scene, this.camera);
        // Call tick again on the next frame
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
      this.object_ID = event.currentTarget.id;
      console.log(event)
      this.fetchDataCategory(this.object_ID);
        const blockRetra = document.getElementById("bloc_retract");
        const blockRetra1 = document.getElementById("bloc_left");
        const bloc_blanc = document.querySelector(".bloc_Blanc");
        
        blockRetra.style.visibility = "visible";
        bloc_blanc.classList.add("translation");
        blockRetra1.style.visibility = "visible";
        blockRetra1.classList.add("left-translation");
      },
      fetchDataCategory(id) {
      axios
        .get(`http://localhost:1337/destinations/${id}`)
        .then((response) => {
          
          this.informations ={
            nomDeVille:response.data.Titre,
            date:response.data.Date,
            descriptif:response.data.Descriptif,
            slug:response.data.Identifiant
          }
          console.log(this.informations)
        });
    },
  },
};
</script>

<style>
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
}
#bloc_retract:after {
  content: "";

  position: absolute;

  background-image: url(/paper-edge.png);
  background-position: right;
  background-repeat: no-repeat;

  width: 32px;
  height: 10px;
  right: -32px;
  height: 100vh;
  top: 0;
}
</style>

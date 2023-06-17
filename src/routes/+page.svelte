<script lang="ts">
    import { onMount } from "svelte";
    import * as THREE from "three";
    import ThreeGlobe from "three-globe";

    import gsap from "gsap";
    import ScrollTrigger from "gsap/ScrollTrigger";

    // Constants
    const SPREAD = 700; // How spread out the stars are
    const TOTAL_STARS = 1500; // How many stars there are

    let canvasElement: HTMLCanvasElement;

    gsap.registerPlugin(ScrollTrigger);

    onMount(() => {
        // Initializing the globe
        const Globe = new ThreeGlobe()
            .globeImageUrl(
                "//unpkg.com/three-globe/example/img/earth-blue-marble.jpg"
            )
            .bumpImageUrl(
                "//unpkg.com/three-globe/example/img/earth-topology.png"
            );
        // Rotate the globe on the diagonal axis
        Globe.rotateY(Math.PI / 2);

        // Custom globe material
        const globeMaterial = Globe.globeMaterial() as THREE.MeshPhongMaterial;
        globeMaterial.bumpScale = 10;

        new THREE.TextureLoader().load(
            "//unpkg.com/three-globe/example/img/earth-water.png",
            (texture) => {
                globeMaterial.specularMap = texture;
                globeMaterial.specular = new THREE.Color("grey");
                globeMaterial.shininess = 100;
            }
        );

        // Setup scene
        const scene = new THREE.Scene();

        scene.add(Globe);

        // Setup camera
        const camera = new THREE.PerspectiveCamera();
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();

        camera.position.x = 0;
        camera.position.y = 120;
        camera.position.z = 125;

        scene.add(camera);

        // Lighting
        const directionalLight = new THREE.DirectionalLight(0xffffff, 0.3);
        directionalLight.position.set(1, 1, 1);
        scene.add(new THREE.AmbientLight(0xcccccc));
        scene.add(directionalLight);

        // Stars
        const starsGeometry = new THREE.BufferGeometry();
        const starMaterial = new THREE.PointsMaterial({ color: 0xffffff });

        const starVertices = [];

        for (let i = 0; i < TOTAL_STARS; i++) {
            const x = THREE.MathUtils.randFloatSpread(SPREAD);
            const y = THREE.MathUtils.randFloatSpread(SPREAD);
            const z = THREE.MathUtils.randFloatSpread(SPREAD);

            starVertices.push(x, y, z);
        }

        starsGeometry.setAttribute(
            "position",
            new THREE.Float32BufferAttribute(starVertices, 3)
        );

        const stars = new THREE.Points(starsGeometry, starMaterial);
        scene.add(stars);

        let renderer: THREE.WebGLRenderer;

        // Render loop
        const animate = () => {
            renderer.setClearColor(0xffffff, 0);
            renderer.render(scene, camera);
            Globe.rotateY(-0.0005);
            requestAnimationFrame(animate);
        };

        // Handles resizing the window
        const resize = () => {
            renderer.setSize(window.innerWidth, window.innerHeight);
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
        };

        const createScene = () => {
            renderer = new THREE.WebGLRenderer({
                antialias: true,
                canvas: canvasElement,
            });
            resize();
            animate();
        };

        window.addEventListener("resize", resize);
        createScene();

        // Zooms into the globe
        gsap.to(camera.position, {
            duration: 1,
            y: 110,
            z: 60,
            ease: "power2.out",
            scrollTrigger: {
                trigger: "#home",
                start: "top top",
                end: "bottom+=1000 center",
                pin: "#test",
                scrub: true,
                // markers: true,
            },
        });

        // Reveals the statistics
        gsap.to("#stats", {
            display: "block",
            opacity: 1,
            y: 10,
            ease: "power2.out",
            scrollTrigger: {
                trigger: "#home",
                start: "top+=900 center",
                end: "top+=1200 center",
                scrub: true,
                // markers: true,
            },
        });
    });
</script>

<svelte:head>
    <title>World Affairs Conference</title>
    <meta
        name="description"
        content="North America's largest and Canada's oldest annual student-run current events conference."
    />
</svelte:head>

<section
    class="pt-[10.5rem] md:pt-44 lg:pt-[13.5rem] text-center relative h-screen"
    id="home"
>
    <div>
        <h1
            class="text-5xl sm:text-6xl md:text-[5.25rem] text-white font-bold mb-5 lg:mb-6"
        >
            World Affairs Conference
        </h1>
        <h3
            class="text-zinc-400 text-md md:text-[1.3rem] mb-8 lg:mb-10 lg:px-40"
        >
            North America's largest and Canada's oldest annual student-run
            current events conference.
        </h3>
        <button
            class="bg-gradient-to-r from-primary to-secondary rounded-full px-10 lg:px-12 py-3.5 text-white text-xs lg:text-base"
        >
            Some Action
        </button>
    </div>
    <div
        class="absolute w-full h-full flex justify-center top-0 -z-50"
        id="test"
    >
        <div
            class="text-5xl absolute top-[36%] left-1/2 transform -translate-x-1/2 opacity-0 hidden transition-opacity text-white"
            id="stats"
        >
            Some Statistic
        </div>
        <canvas bind:this={canvasElement} />
    </div>
</section>
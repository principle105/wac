<script lang="ts">
    import { onMount } from "svelte";
    import * as THREE from "three";
    import ThreeGlobe from "three-globe";

    import gsap from "gsap";
    import ScrollTrigger from "gsap/ScrollTrigger";

    // Importing the speaker images
    import mlk from "$lib/speakers/mlk.jpg";
    import edwardSnowden from "$lib/speakers/edward_snowden.jpg";
    import ckHoffler from "$lib/speakers/ck_hoffler.jpg";
    import scottGalloway from "$lib/speakers/scott_galloway.jpg";
    import marcGarneau from "$lib/speakers/marc_garneau.jpg";
    import mehdiHasan from "$lib/speakers/mehdi_hasan.jpg";
    import johnStackhouse from "$lib/speakers/john_stackhouse.jpg";
    import davidOwen from "$lib/speakers/david_owen.jpg";
    import geoffreyHinton from "$lib/speakers/geoffrey_hinton.jpg";

    // Constants
    const SPREAD: number = 700; // How spread out the stars are
    const TOTAL_STARS: number = 1500; // How many stars there are

    interface Speaker {
        name: string;
        title: string;
        image: string;
        large?: boolean;
    }

    const speakers: Speaker[] = [
        {
            name: "Martin Luther King III",
            title: "American Human Rights Activist",
            image: mlk,
            large: true,
        },
        {
            name: "Edward Snowden",
            title: "Former NSA Consultant & Whistleblower",
            image: edwardSnowden,
        },
        {
            name: "Ck Hoffler",
            title: "CEO of The CK Hoffler Firm",
            image: ckHoffler,
        },
        {
            name: "Scott Galloway",
            title: "Professor of Marketing at NYU Stern School of Business",
            image: scottGalloway,
        },
        {
            name: "Marc Garneau",
            title: "Former Canadian Astronaut",
            image: marcGarneau,
        },
        {
            name: "Mehdi Hasan",
            title: "British-American Political Journalist, Broadcaster and Author",
            image: mehdiHasan,
        },
        {
            name: "John Stackhouse",
            title: "Former Editor-in-Chief of The Globe and Mail",
            image: johnStackhouse,
        },
        {
            name: "David Owen",
            title: "Former British Foreign Secretary",
            image: davidOwen,
        },
        {
            name: "Dr Geoffrey Hinton",
            title: "2018 recipient of the Turing Award for Computer Science",
            image: geoffreyHinton,
        },
    ];

    let canvasElement: HTMLCanvasElement;

    gsap.registerPlugin(ScrollTrigger);

    onMount(() => {
        // Initializing the globe

        // Make the atmosphere stronger
        const Globe = new ThreeGlobe({ animateIn: false })
            .globeImageUrl("//i.imgur.com/5bEEM5o.jpg")
            .bumpImageUrl(
                "//unpkg.com/three-globe/example/img/earth-topology.png"
            )
            .atmosphereColor("rgba(82, 167, 220, 1)")
            .atmosphereAltitude(0.12);

        // Rotate the globe on the diagonal axis
        Globe.rotateY(Math.PI / 2);

        // Custom globe material
        const globeMaterial = Globe.globeMaterial() as THREE.MeshPhongMaterial;
        globeMaterial.bumpScale = 10;

        new THREE.TextureLoader().load(
            "//unpkg.com/three-globe/example/img/earth-water.png",
            (texture) => {
                globeMaterial.specularMap = texture;
                // globeMaterial.specular = new THREE.Color("grey");
                // globeMaterial.shininess = 20;
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

        // Timeline for camera movement
        gsap.timeline({
            scrollTrigger: {
                trigger: "#home",
                start: "top top",
                end: "bottom+=13600 center",
                pin: "#home",
                scrub: true,
                // markers: true,
            },
        })
            .to(camera.position, {
                duration: 1,
                y: 110,
                z: 60,
                ease: "power2.out",
            })
            // Make the globe zoom out and position to the left side of the screen
            .to(camera.position, {
                duration: 1,
                x: 175,
                y: 10,
                z: 150,
                ease: "power2.out",
                delay: 0.25,
            })
            .to(camera.position, {
                delay: 3,
            })
            .to(camera.position, {
                duration: 3,
                x: 15,
                y: 10,
                z: 150,
                ease: "power2.out",
                delay: 0.25,
            });

        const textTimeline = gsap.timeline({
            scrollTrigger: {
                trigger: "#container",
                start: "top+=900 center",
                end: "top+=2100 center",
                scrub: true,
                // markers: true,
            },
        });

        // Reveals the statistics
        textTimeline
            .to("#stats", {
                opacity: 1,
                y: 10,
                duration: 4,
                display: "block",
            })
            .to("#stats", {
                opacity: 0,
                duration: 1,
                y: -10,
                display: "hidden",
                delay: 5,
            });

        const speakerTimeline = gsap.timeline({
            scrollTrigger: {
                trigger: "#stats",
                start: "top+=2900 center",
                end: "top+=9000 center",
                scrub: true,
                // markers: true,
            },
        });

        speakerTimeline.to("#speakers", {
            display: "flex",
            opacity: 1,
        });

        // Reveals the featured speakers
        speakers.forEach((_, index) => {
            speakerTimeline.to("#speaker-" + index, {
                opacity: 1,
                y: 10,
                ease: "power2.out",
            });
        });

        speakerTimeline.to("#speakers", {
            opacity: 0,
            duration: 1,
            y: -10,
            display: "hidden",
            delay: 2,
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
    class="pt-[10rem] md:pt-44 lg:pt-[13.5rem] text-center flex flex-col items-center h-screen w-screen absolute top-0 left-0"
>
    <div>
        <h1
            class="text-[2.9rem] leading-none sm:text-6xl lg:text-[5.25rem] text-white font-bold mb-5 lg:mb-6"
        >
            World Affairs Conference
        </h1>
        <h3 class="text-zinc-400 text-md lg:text-[1.3rem] mb-4 md:px-48">
            North America's largest and Canada's oldest annual student-run
            current events conference.
        </h3>
    </div>

    <div class="flex gap-4 mb-6 text-xl lg:text-2xl">
        <span class="text-primary">#BeThere</span>
        <span class="text-secondary">#RollWAC</span>
    </div>

    <div class="flex gap-1.5 flex-col sm:flex-row">
        <div class="relative">
            <div
                class="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none"
            >
                <svg
                    aria-hidden="true"
                    class="w-5 h-5 text-zinc-400"
                    fill="currentColor"
                    viewBox="0 0 20 20"
                    xmlns="http://www.w3.org/2000/svg"
                >
                    <path
                        d="M2.003 5.884L10 9.882l7.997-3.998A2 2 0 0016 4H4a2 2 0 00-1.997 1.884z"
                    />
                    <path
                        d="M18 8.118l-8 4-8-4V14a2 2 0 002 2h12a2 2 0 002-2V8.118z"
                    />
                </svg>
            </div>
            <input
                type="text"
                class="border text-sm rounded-lg block w-56 md:w-80 pl-10 p-2.5 bg-zinc-700 border-zinc-600 placeholder-zinc-400 text-white focus:ring-zinc-400 focus:border-zinc-400 outline-none"
                placeholder="name@school.com"
            />
        </div>
        <button
            class="bg-gradient-to-r from-primary to-secondary rounded-lg px-6 py-2 text-white text-sm"
        >
            Get Notified
        </button>
    </div>
</section>

<section
    class="pt-[10rem] md:pt-44 lg:pt-[13.5rem] text-center relative h-screen"
    id="home"
>
    <div
        class="absolute w-full h-screen flex justify-center top-0 -z-50"
        id="container"
    >
        <div
            class="absolute top-[20%] sm:top-1/4 left-1/2 transform -translate-x-1/2 -translate-y-[20%] sm:-translate-y-1/4 w-full opacity-0 hidden transition-opacity"
            id="stats"
        >
            <h3 class="text-lg sm:text-xl md:text-2xl text-white mb-10">
                WAC has reached:
            </h3>
            <dl
                class="max-w-screen-md gap-8 mx-auto text-white flex justify-between flex-wrap text-center [&>*]:mx-auto"
            >
                <div class="flex flex-col items-center justify-center mx-auto">
                    <dt
                        class="mb-2 text-5xl sm:text-6xl md:text-[4.75rem] font-bold"
                    >
                        10k+
                    </dt>
                    <dd class="text-zinc-400">students</dd>
                </div>
                <div class="flex flex-col items-center justify-center mx-auto">
                    <dt
                        class="mb-2 text-5xl sm:text-6xl md:text-[4.75rem] font-bold"
                    >
                        35+
                    </dt>
                    <dd class="text-zinc-400">countries</dd>
                </div>
                <div class="flex flex-col items-center justify-center mx-auto">
                    <dt
                        class="mb-2 text-5xl sm:text-6xl md:text-[4.75rem] font-bold"
                    >
                        80+
                    </dt>
                    <dd class="text-zinc-400">schools</dd>
                </div>
            </dl>
        </div>
        <div
            class="absolute w-full h-screen hidden opacity-0 text-left gap-4 sm:gap-8 flex-col"
            id="speakers"
        >
            <h2
                class="text-center text-3xl sm:text-5xl font-bold text-white mt-8 sm:mt-14"
            >
                Past Speakers
            </h2>

            <div
                class="grid grid-cols-3 lg:grid-cols-4 xl:grid-cols-6 gap-6 auto-rows-fr grow"
            >
                {#each speakers as speaker, index}
                    <div
                        id="speaker-{index}"
                        class="opacity-0 transition-opacity col-span-1 row-span-1 {speaker.large &&
                            'lg:col-span-2 lg:row-span-2'}"
                    >
                        <div class="rounded-md h-full relative overflow-hidden">
                            <img
                                src={speaker.image}
                                alt="{speaker.name}'s Headshot"
                                class="w-full rounded-lg h-full object-cover"
                            />

                            <div
                                class="absolute bottom-0 w-full p-2 backdrop-blur-md bg-zinc-800/30 rounded-tr-md hidden sm:block"
                            >
                                <h3
                                    class="font-semibold text-white text-sm {speaker.large &&
                                        'lg:text-xl'}"
                                >
                                    {speaker.name}
                                </h3>
                                <p
                                    class="text-zinc-300 text-[0.6rem] {speaker.large &&
                                        'lg:text-xs'}"
                                >
                                    {speaker.title}
                                </p>
                            </div>
                        </div>
                    </div>
                {/each}
            </div>
            <div class="mt-6" />
        </div>
        <canvas bind:this={canvasElement} />
    </div>
</section>

<section class="w-full h-screen bg-orange-300" />
<section class="w-full h-screen bg-purple-400" />
<section class="w-full h-screen bg-green-400" />

<script lang="ts">
    import { onMount } from "svelte";
    import * as THREE from "three";
    import ThreeGlobe from "three-globe";

    import gsap from "gsap";
    import { ScrollTrigger } from "gsap/ScrollTrigger";

    import { Swiper, SwiperSlide } from "swiper/svelte";
    import "swiper/css";

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

    // Importing the school images
    import ucc from "$lib/logos/ucc.png";
    import branksome from "$lib/logos/branksome.png";

    // Constants
    const SPREAD: number = 700; // How spread out the stars are
    const TOTAL_STARS: number = 1500; // How many stars there are

    interface Speaker {
        name: string;
        title: string;
        image: string;
        specialRole?: string;
    }

    const speakers: Speaker[] = [
        {
            name: "Martin Luther King III",
            title: "American Human Rights Activist",
            image: mlk,
            specialRole: "Keynote Speaker",
        },
        {
            name: "Edward Snowden",
            title: "Former NSA Consultant & Whistleblower",
            image: edwardSnowden,
            specialRole: "Criminal Speaker",
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
    let gsapScope: Element;

    if (typeof window !== "undefined") {
        gsap.registerPlugin(ScrollTrigger);
    }

    onMount(() => {
        // Initializing the globe
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

        const starVertices: Array<number> = [];

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
            renderer.render(scene, camera);
            Globe.rotateY(-0.0005);
            requestAnimationFrame(animate);
        };

        // Handles resizing the window
        const handleWindowResize = () => {
            renderer.setSize(window.innerWidth, window.innerHeight);
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
        };

        const createScene = () => {
            renderer = new THREE.WebGLRenderer({
                antialias: true,
                canvas: canvasElement,
                alpha: true,
            });
            renderer.setPixelRatio(window.devicePixelRatio);
            renderer.setSize(window.innerWidth, window.innerHeight);
            animate();
        };

        window.addEventListener("resize", handleWindowResize);
        createScene();

        const ctx = gsap.context(() => {
            // Timeline for camera movement
            gsap.timeline({
                scrollTrigger: {
                    trigger: "#home",
                    start: "top top",
                    end: "bottom+=6700 center",
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
                // Make the globe zoom in and position to the left side of the screen
                .to(camera.position, {
                    duration: 0.75,
                    x: 125,
                    y: 10,
                    z: 150,
                    ease: "power2.out",
                    delay: 0.25,
                })
                .to(camera.position, {
                    delay: 0.75,
                })
                .to(camera.position, {
                    duration: 1.25,
                    x: 0,
                    y: -75,
                    z: 175,
                    ease: "linear",
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
                    start: "top+=3300 center",
                    end: "top+=5700 center",
                    scrub: true,
                    // markers: true,
                },
            });

            speakerTimeline.from("#speakers", {
                display: "hidden",
                opacity: 0,
                y: 10,
                duration: 1,
                ease: "easeOut",
            });

            speakerTimeline.to("#speakers", {
                opacity: 0,
                duration: 1,
                y: -10,
                display: "hidden",
                delay: 1.5,
            });

            gsap.to(starVertices, {
                scrollTrigger: {
                    trigger: "#transitionPart",
                    start: "top-=100% bottom",
                    end: "top bottom",
                    scrub: true,
                    // markers: true,
                    onUpdate: (self) => {
                        // Removing last 10 items from starVertices array on scrolltrigger on each update
                        const starsRendered =
                            starVertices.length -
                            Math.floor(starVertices.length * self.progress);

                        starsGeometry.setAttribute(
                            "position",
                            new THREE.Float32BufferAttribute(
                                starVertices.slice(0, starsRendered),
                                3
                            )
                        );
                    },
                },
            });

            gsap.to(gsapScope, {
                scrollTrigger: {
                    trigger: "#transitionPart",
                    start: "top+=50% bottom",
                    end: "bottom bottom",
                    scrub: true,
                    // markers: true,
                },
                backgroundColor: "#ffffff",
                ease: "linear",
            });

            return () => ctx.revert();
        }, gsapScope);
    });
</script>

<svelte:head>
    <title>World Affairs Conference</title>
    <meta
        name="description"
        content="North America's largest and Canada's oldest annual student-run current events conference."
    />
</svelte:head>

<div bind:this={gsapScope}>
    <section
        class="pt-[10rem] md:pt-44 lg:pt-[13.5rem] text-center flex flex-col items-center h-screen w-screen absolute top-0 left-0 z-30"
    >
        <div class="w-5/6 mx-auto">
            <h1
                class="text-[2.9rem] leading-none sm:text-6xl lg:text-[5.35rem] text-white font-bold mb-5 lg:mb-6 tracking-tight"
            >
                World Affairs Conference
            </h1>
            <p
                class="text-zinc-400 text-md lg:text-[1.3rem] mb-3.5 md:px-32 lg:px-48"
            >
                North America's largest and Canada's oldest annual student-run
                current events conference.
            </p>
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
            class="absolute w-full h-screen flex justify-center inset-0 -z-50"
            id="container"
        >
            <div
                class="absolute top-[20%] sm:top-1/4 left-1/2 transform -translate-x-1/2 -translate-y-[20%] sm:-translate-y-1/4 w-5/6 mx-auto opacity-0 hidden transition-opacity"
                id="stats"
            >
                <h2 class="text-lg sm:text-xl md:text-2xl text-white mb-10">
                    WAC has reached:
                </h2>
                <dl
                    class="max-w-screen-md gap-8 mx-auto text-white flex justify-between flex-wrap text-center [&>*]:mx-auto"
                >
                    <div
                        class="flex flex-col items-center justify-center mx-auto"
                    >
                        <dt
                            class="mb-2 text-5xl sm:text-6xl md:text-[4.75rem] font-bold"
                        >
                            10k+
                        </dt>
                        <dd class="text-zinc-400">students</dd>
                    </div>
                    <div
                        class="flex flex-col items-center justify-center mx-auto"
                    >
                        <dt
                            class="mb-2 text-5xl sm:text-6xl md:text-[4.75rem] font-bold"
                        >
                            35+
                        </dt>
                        <dd class="text-zinc-400">countries</dd>
                    </div>
                    <div
                        class="flex flex-col items-center justify-center mx-auto"
                    >
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
                class="absolute w-full h-screen flex text-left gap-4 sm:gap-8 flex-col py-14"
                id="speakers"
            >
                <h2
                    class="text-center text-4xl sm:text-6xl font-bold text-white tracking-tight"
                >
                    2023 Speakers
                </h2>
                <div class="self-end text-white">
                    <button>Left</button>
                    <button>Right</button>
                </div>
                <div class="grow overflow-hidden relative">
                    <div
                        class="absolute top-0 left-1/2 transform -translate-x-1/2 h-full w-[130vw]"
                    >
                        <Swiper
                            spaceBetween={16}
                            slidesPerView={3}
                            class="flex gap-6 w-full h-full"
                            loop={true}
                            breakpoints={{
                                "@0.7": {
                                    slidesPerView: 4,
                                },
                                "@0.9": {
                                    slidesPerView: 5,
                                },
                                "@1.00": {
                                    slidesPerView: 6, // TODO: use pixel breakpoints
                                },
                            }}
                        >
                            {#each speakers as speaker}
                                <SwiperSlide
                                    class="rounded-md relative overflow-hidden"
                                >
                                    <img
                                        src={speaker.image}
                                        alt="{speaker.name}'s Headshot"
                                        class="w-full rounded-lg h-full object-cover transition-all"
                                    />

                                    <div
                                        class="absolute bottom-0 w-full backdrop-blur-md bg-zinc-800/30 rounded-tr-md h-20 px-3 flex"
                                    >
                                        <div class="my-auto">
                                            <h3
                                                class="font-semibold text-white text-lg md:text-xl"
                                            >
                                                {speaker.name}
                                            </h3>
                                            <p
                                                class="text-zinc-300 text-[0.6rem] md:text-xs"
                                            >
                                                {speaker.title}
                                            </p>
                                        </div>
                                    </div>
                                    <div
                                        class="absolute top-3 left-3 p-2 bg-zinc-900/50 rounded-md text-xs text-white"
                                    >
                                        {speaker.specialRole ||
                                            "Plenary Speaker"}
                                    </div>
                                </SwiperSlide>
                            {/each}
                        </Swiper>
                    </div>
                </div>
                <a
                    class="font-semibold text-zinc-200 text-center sm:text-lg"
                    href="/speakers"
                    target="_blank"
                    rel="noreferrer"
                >
                    View the Rest of Our 2023 Speakers →
                </a>
            </div>
            <canvas bind:this={canvasElement} />
        </div>
    </section>

    <section class="w-screen h-screen" id="transitionPart" />

    <section class="w-screen bg-white py-32">
        <h3
            class="text-3xl md:text-4xl font-semibold uppercase text-center mb-5 tracking-tight"
        >
            Hosted By
        </h3>

        <div
            class="flex flex-col md:flex-row gap-20 items-center justify-center mb-10"
        >
            <img
                src={branksome}
                alt="Branksome Hall Logo"
                class="h-full w-auto"
            />
            <img
                src={ucc}
                alt="Upper Canada College Logo"
                class="h-full w-auto"
            />
        </div>
    </section>

    <section class="w-screen h-screen">
        <iframe
            src="https://www.youtube.com/embed/UfDBOA47oN4"
            class="w-full aspect-auto h-full sm:aspect-video sm:h-auto m-auto"
            title="YouTube video player"
            frameborder="0"
            allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
            allowfullscreen
        />
    </section>

    <section
        class="w-screen bg-zinc-900 px-32 py-20 flex justify-between items-center"
        id="action"
    >
        <h2 class="uppercase flex flex-col">
            <span
                class="text-3xl md:text-4xl text-zinc-300 font-semibold tracking-tighter"
            >
                Stay
            </span>
            <span
                class="text-3xl md:text-7xl text-secondary font-bold tracking-tighter"
            >
                Updated
            </span>
        </h2>
        <div>
            <h4 class="text-white text-2xl font-semibold mb-5">
                Sign up for WAC updates
            </h4>
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
        </div>
    </section>
</div>

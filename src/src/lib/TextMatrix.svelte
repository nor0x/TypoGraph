<script lang="ts">
  import { onMount } from "svelte";
  import html2canvas from "html2canvas";
  import { fade } from "svelte/transition";

  const urlParams = new URLSearchParams(window.location.search);
  let text = urlParams.get("t") || "";
  let matrix = [];
  const colors = ["#eeeeee", "#9be9a8", "#40c463", "#30a14e", "#216e39"];
  const rows = 7;
  const cols = 52;
  let matrixRef;
  let showToast = false;
  let matrixScale = 1;

  const charMap = {
    " ": [],
    A: [
      [1, 1, 1],
      [1, 0, 1],
      [1, 1, 1],
      [1, 0, 1],
      [1, 0, 1],
    ],
    B: [
      [1, 1, 0],
      [1, 0, 1],
      [1, 1, 0],
      [1, 0, 1],
      [1, 1, 0],
    ],
    C: [
      [1, 1, 1],
      [1, 0, 0],
      [1, 0, 0],
      [1, 0, 0],
      [1, 1, 1],
    ],
    D: [
      [1, 1, 0],
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
      [1, 1, 0],
    ],
    E: [
      [1, 1, 1],
      [1, 0, 0],
      [1, 1, 0],
      [1, 0, 0],
      [1, 1, 1],
    ],
    F: [
      [1, 1, 1],
      [1, 0, 0],
      [1, 1, 0],
      [1, 0, 0],
      [1, 0, 0],
    ],
    G: [
      [1, 1, 1],
      [1, 0, 0],
      [1, 0, 1],
      [1, 0, 1],
      [1, 1, 1],
    ],
    H: [
      [1, 0, 1],
      [1, 0, 1],
      [1, 1, 1],
      [1, 0, 1],
      [1, 0, 1],
    ],
    I: [
      [1, 1, 1],
      [0, 1, 0],
      [0, 1, 0],
      [0, 1, 0],
      [1, 1, 1],
    ],
    J: [
      [0, 0, 1],
      [0, 0, 1],
      [0, 0, 1],
      [1, 0, 1],
      [1, 1, 1],
    ],
    K: [
      [1, 0, 1],
      [1, 0, 1],
      [1, 1, 0],
      [1, 0, 1],
      [1, 0, 1],
    ],
    L: [
      [1, 0, 0],
      [1, 0, 0],
      [1, 0, 0],
      [1, 0, 0],
      [1, 1, 1],
    ],
    M: [
      [1, 0, 1],
      [1, 1, 1],
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
    ],
    N: [
      [1, 0, 1],
      [1, 1, 1],
      [1, 1, 1],
      [1, 0, 1],
      [1, 0, 1],
    ],
    O: [
      [1, 1, 1],
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
      [1, 1, 1],
    ],
    P: [
      [1, 1, 1],
      [1, 0, 1],
      [1, 1, 1],
      [1, 0, 0],
      [1, 0, 0],
    ],
    Q: [
      [1, 1, 1],
      [1, 0, 1],
      [1, 0, 1],
      [1, 1, 1],
      [0, 0, 1],
    ],
    R: [
      [1, 1, 1],
      [1, 0, 1],
      [1, 1, 0],
      [1, 0, 1],
      [1, 0, 1],
    ],
    S: [
      [1, 1, 1],
      [1, 0, 0],
      [1, 1, 1],
      [0, 0, 1],
      [1, 1, 1],
    ],
    T: [
      [1, 1, 1],
      [0, 1, 0],
      [0, 1, 0],
      [0, 1, 0],
      [0, 1, 0],
    ],
    U: [
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
      [1, 1, 1],
    ],
    V: [
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
      [0, 1, 0],
    ],
    W: [
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
      [1, 1, 1],
      [1, 0, 1],
    ],
    X: [
      [1, 0, 1],
      [1, 0, 1],
      [0, 1, 0],
      [1, 0, 1],
      [1, 0, 1],
    ],
    Y: [
      [1, 0, 1],
      [1, 0, 1],
      [0, 1, 0],
      [0, 1, 0],
      [0, 1, 0],
    ],
    Z: [
      [1, 1, 1],
      [0, 0, 1],
      [0, 1, 0],
      [1, 0, 0],
      [1, 1, 1],
    ],
    a: [
      [0, 0, 0],
      [0, 1, 1],
      [1, 0, 1],
      [1, 0, 1],
      [0, 1, 1],
    ],
    b: [
      [1, 0, 0],
      [1, 1, 0],
      [1, 0, 1],
      [1, 0, 1],
      [1, 1, 0],
    ],
    c: [
      [0, 0, 0],
      [0, 1, 1],
      [1, 0, 0],
      [1, 0, 0],
      [0, 1, 1],
    ],
    d: [
      [0, 0, 1],
      [0, 1, 1],
      [1, 0, 1],
      [1, 0, 1],
      [0, 1, 1],
    ],
    e: [
      [0, 1, 0],
      [1, 0, 1],
      [1, 1, 1],
      [1, 0, 0],
      [0, 1, 1],
    ],
    f: [
      [0, 1, 1],
      [1, 0, 0],
      [1, 1, 0],
      [1, 0, 0],
      [1, 0, 0],
    ],
    g: [
      [0, 1, 1],
      [1, 0, 1],
      [0, 1, 1],
      [0, 0, 1],
      [1, 1, 0],
    ],
    h: [
      [1, 0, 0],
      [1, 1, 0],
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
    ],
    i: [
      [0, 1, 0],
      [0, 0, 0],
      [0, 1, 0],
      [0, 1, 0],
      [0, 1, 0],
    ],
    j: [
      [0, 0, 1],
      [0, 0, 0],
      [0, 0, 1],
      [1, 0, 1],
      [0, 1, 0],
    ],
    k: [
      [1, 0, 0],
      [1, 0, 1],
      [1, 1, 0],
      [1, 0, 1],
      [1, 0, 1],
    ],
    l: [
      [0, 1, 0],
      [0, 1, 0],
      [0, 1, 0],
      [0, 1, 0],
      [0, 1, 1],
    ],
    m: [
      [0, 0, 0],
      [1, 1, 1],
      [1, 1, 1],
      [1, 0, 1],
      [1, 0, 1],
    ],
    n: [
      [0, 0, 0],
      [1, 1, 0],
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
    ],
    o: [
      [0, 0, 0],
      [0, 1, 0],
      [1, 0, 1],
      [1, 0, 1],
      [0, 1, 0],
    ],
    p: [
      [0, 0, 0],
      [1, 1, 0],
      [1, 0, 1],
      [1, 1, 0],
      [1, 0, 0],
    ],
    q: [
      [0, 0, 0],
      [0, 1, 1],
      [1, 0, 1],
      [0, 1, 1],
      [0, 0, 1],
    ],
    r: [
      [0, 0, 0],
      [1, 1, 0],
      [1, 0, 1],
      [1, 0, 0],
      [1, 0, 0],
    ],
    s: [
      [0, 0, 0],
      [0, 1, 1],
      [1, 1, 0],
      [0, 0, 1],
      [1, 1, 0],
    ],
    t: [
      [0, 1, 0],
      [1, 1, 1],
      [0, 1, 0],
      [0, 1, 0],
      [0, 1, 1],
    ],
    u: [
      [0, 0, 0],
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
      [0, 1, 1],
    ],
    v: [
      [0, 0, 0],
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
      [0, 1, 0],
    ],
    w: [
      [0, 0, 0],
      [1, 0, 1],
      [1, 0, 1],
      [1, 1, 1],
      [0, 1, 0],
    ],
    x: [
      [0, 0, 0],
      [1, 0, 1],
      [0, 1, 0],
      [0, 1, 0],
      [1, 0, 1],
    ],
    y: [
      [0, 0, 0],
      [1, 0, 1],
      [0, 1, 1],
      [0, 0, 1],
      [1, 1, 0],
    ],
    z: [
      [0, 0, 0],
      [1, 1, 1],
      [0, 1, 0],
      [1, 0, 0],
      [1, 1, 1],
    ],
    "0": [
      [1, 1, 1],
      [1, 0, 1],
      [1, 0, 1],
      [1, 0, 1],
      [1, 1, 1],
    ],
    "1": [
      [0, 1, 0],
      [1, 1, 0],
      [0, 1, 0],
      [0, 1, 0],
      [1, 1, 1],
    ],
    "2": [
      [1, 1, 1],
      [0, 0, 1],
      [1, 1, 1],
      [1, 0, 0],
      [1, 1, 1],
    ],
    "3": [
      [1, 1, 1],
      [0, 0, 1],
      [0, 1, 1],
      [0, 0, 1],
      [1, 1, 1],
    ],
    "4": [
      [1, 0, 1],
      [1, 0, 1],
      [1, 1, 1],
      [0, 0, 1],
      [0, 0, 1],
    ],
    "5": [
      [1, 1, 1],
      [1, 0, 0],
      [1, 1, 1],
      [0, 0, 1],
      [1, 1, 1],
    ],
    "6": [
      [1, 1, 1],
      [1, 0, 0],
      [1, 1, 1],
      [1, 0, 1],
      [1, 1, 1],
    ],
    "7": [
      [1, 1, 1],
      [0, 0, 1],
      [0, 1, 0],
      [0, 1, 0],
      [0, 1, 0],
    ],
    "8": [
      [1, 1, 1],
      [1, 0, 1],
      [1, 1, 1],
      [1, 0, 1],
      [1, 1, 1],
    ],
    "9": [
      [1, 1, 1],
      [1, 0, 1],
      [1, 1, 1],
      [0, 0, 1],
      [1, 1, 1],
    ],
    ".": [
      [0, 0, 0],
      [0, 0, 0],
      [0, 0, 0],
      [0, 0, 0],
      [0, 1, 0],
    ],
    ",": [
      [0, 0, 0],
      [0, 0, 0],
      [0, 0, 0],
      [0, 1, 0],
      [1, 0, 0],
    ],
    "!": [
      [0, 1, 0],
      [0, 1, 0],
      [0, 1, 0],
      [0, 0, 0],
      [0, 1, 0],
    ],
    "?": [
      [1, 1, 1],
      [0, 0, 1],
      [0, 1, 0],
      [0, 0, 0],
      [0, 1, 0],
    ],
    "@": [
      [1, 1, 1],
      [1, 0, 1],
      [1, 1, 1],
      [1, 0, 0],
      [1, 1, 1],
    ],
    "#": [
      [1, 0, 1],
      [1, 1, 1],
      [1, 0, 1],
      [1, 1, 1],
      [1, 0, 1],
    ],
    $: [
      [0, 1, 1],
      [1, 1, 0],
      [0, 1, 0],
      [0, 1, 1],
      [1, 1, 0],
    ],
    "%": [
      [1, 0, 1],
      [0, 0, 1],
      [0, 1, 0],
      [1, 0, 0],
      [1, 0, 1],
    ],
    "&": [
      [1, 1, 0],
      [1, 1, 0],
      [1, 1, 1],
      [1, 0, 1],
      [1, 1, 1],
    ],
    "*": [
      [1, 0, 1],
      [0, 1, 0],
      [1, 1, 1],
      [0, 1, 0],
      [1, 0, 1],
    ],
    "(": [
      [0, 1, 0],
      [1, 0, 0],
      [1, 0, 0],
      [1, 0, 0],
      [0, 1, 0],
    ],
    ")": [
      [0, 1, 0],
      [0, 0, 1],
      [0, 0, 1],
      [0, 0, 1],
      [0, 1, 0],
    ],
    "-": [
      [0, 0, 0],
      [0, 0, 0],
      [1, 1, 1],
      [0, 0, 0],
      [0, 0, 0],
    ],
    "+": [
      [0, 0, 0],
      [0, 1, 0],
      [1, 1, 1],
      [0, 1, 0],
      [0, 0, 0],
    ],
    "=": [
      [0, 0, 0],
      [1, 1, 1],
      [0, 0, 0],
      [1, 1, 1],
      [0, 0, 0],
    ],
    "/": [
      [0, 0, 1],
      [0, 0, 1],
      [0, 1, 0],
      [1, 0, 0],
      [1, 0, 0],
    ],
    "\\": [
      [1, 0, 0],
      [1, 0, 0],
      [0, 1, 0],
      [0, 0, 1],
      [0, 0, 1],
    ],
    "'": [
      [0, 1, 0],
      [0, 1, 0],
      [0, 0, 0],
      [0, 0, 0],
      [0, 0, 0],
    ],
    '"': [
      [1, 0, 1],
      [1, 0, 1],
      [0, 0, 0],
      [0, 0, 0],
      [0, 0, 0],
    ],
    ":": [
      [0, 0, 0],
      [0, 1, 0],
      [0, 0, 0],
      [0, 1, 0],
      [0, 0, 0],
    ],
    ";": [
      [0, 0, 0],
      [0, 1, 0],
      [0, 0, 0],
      [0, 1, 0],
      [1, 0, 0],
    ],
    "<": [
      [0, 0, 1],
      [0, 1, 0],
      [1, 0, 0],
      [0, 1, 0],
      [0, 0, 1],
    ],
    ">": [
      [1, 0, 0],
      [0, 1, 0],
      [0, 0, 1],
      [0, 1, 0],
      [1, 0, 0],
    ],
  };

  const getTextMatrix = () => {
    const newMatrix = Array(rows)
      .fill(null)
      .map(() => Array(cols).fill({ active: false, color: colors[0] }));
    const letters = text.split("");
    let letterIndex = 0;

    for (let letter of letters) {
      if (letterIndex >= cols - 3) break; // Prevent overflow

      const pattern = charMap[letter] || [];
      if (pattern.length > 0) {
        for (let i = 0; i < pattern.length; i++) {
          for (let j = 0; j < pattern[i].length; j++) {
            if (pattern[i][j]) {
              const randomColor =
                colors[Math.floor(Math.random() * (colors.length - 1)) + 1];
              newMatrix[i + 1][letterIndex + j] = {
                active: true,
                color: randomColor,
              };
            }
          }
        }
        letterIndex += 4; // Space between letters
      } else {
        letterIndex += 2; // Space for unknown characters
      }
    }

    return newMatrix;
  };

  $: matrix = getTextMatrix();

  $: if (text) {
    matrix = getTextMatrix();
  }

  const copyToClipboard = async () => {
    if (matrixRef) {
      try {
        const canvas = await html2canvas(matrixRef);
        canvas.toBlob((blob) => {
          navigator.clipboard.write([
            new ClipboardItem({
              "image/png": blob,
            }),
          ]);
        });
        showToast = true; // Show toast
        setTimeout(() => (showToast = false), 2000);
      } catch (err) {
        console.error("Failed to copy image:", err);
      }
    }
  };

  onMount(() => {
    const updateMatrixScale = () => {
      if (matrixRef) {
        const screenWidth = window.innerWidth;
        //matrixWidth should be the width of .matrix-grid
        let matrixGrid = matrixRef.querySelector(".matrix-grid");
        let matrixWidth = matrixGrid.offsetWidth;

        matrixWidth = matrixWidth + 24;

        if (screenWidth < matrixWidth) {
          matrixScale = screenWidth / matrixWidth;
        } else {
          matrixScale = 1; // Reset scale for larger screens
        }
      }
    };

    updateMatrixScale();
    window.addEventListener("resize", updateMatrixScale);

    return () => {
      window.removeEventListener("resize", updateMatrixScale);
    };
  });
</script>

<div
  class="flex flex-col items-center justify-center p-1 md:p-6 bg-gray-100 min-h-screen"
>
  <h1
    class="text-[3rem] font-bold text-gray-800"
    style="font-family: 'Fascinate Inline', cursive;"
  >
    TypoGraph
  </h1>
  <h2 class="text-md text-gray-600 mb-6 mx-auto italic">
    your text as a GitHub contribution graph
  </h2>
  <!-- Matrix Container -->
  <div
    bind:this={matrixRef}
    class="p-4 bg-white rounded-lg shadow-md overflow-hidden mb-6 flex max-w-full content-center"
  >
    <a
      href="https://github.com/nor0x/TypoGraph"
      target="_blank"
      class="github-corner"
      aria-label="View source on GitHub"
    >
      <svg
        width="80"
        height="80"
        viewBox="0 0 250 250"
        style="fill:#40c463; color:#fff; position: absolute; top: 0; border: 0; right: 0;"
        aria-hidden="true"
      >
        <path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path>
        <path
          d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2"
          fill="currentColor"
          style="transform-origin: 130px 106px;"
          class="octo-arm"
        ></path>
        <path
          d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z"
          fill="currentColor"
          class="octo-body"
        ></path>
      </svg>
    </a>
    <div
      class="matrix-grid grid gap-0.5"
      style="grid-template-columns: repeat(52, 10px);"
    >
      {#each matrix as row, rowIndex}
        {#each row as cell, colIndex}
          <div
            class="w-2.5 h-2.5 rounded-[2px]"
            style="background-color: {cell.color};"
          ></div>
        {/each}
      {/each}
    </div>
  </div>

  <!-- Input and Button -->
  <div class="flex items-center w-full max-w-md">
    <input
      type="text"
      placeholder="Enter text here..."
      bind:value={text}
      class="flex-1 px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-[#40c463] focus:border-[#40c463]"
    />
    <button
      on:click={copyToClipboard}
      class="flex items-center gap-2 px-4 py-2 bg-green-500 text-white font-semibold rounded-md shadow hover:bg-green-600 focus:outline-none focus:ring-2 focus:ring-green-400"
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke-width="1.5"
        stroke="currentColor"
        class="size-6"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          d="M8.25 7.5V6.108c0-1.135.845-2.098 1.976-2.192.373-.03.748-.057 1.123-.08M15.75 18H18a2.25 2.25 0 0 0 2.25-2.25V6.108c0-1.135-.845-2.098-1.976-2.192a48.424 48.424 0 0 0-1.123-.08M15.75 18.75v-1.875a3.375 3.375 0 0 0-3.375-3.375h-1.5a1.125 1.125 0 0 1-1.125-1.125v-1.5A3.375 3.375 0 0 0 6.375 7.5H5.25m11.9-3.664A2.251 2.251 0 0 0 15 2.25h-1.5a2.251 2.251 0 0 0-2.15 1.586m5.8 0c.065.21.1.433.1.664v.75h-6V4.5c0-.231.035-.454.1-.664M6.75 7.5H4.875c-.621 0-1.125.504-1.125 1.125v12c0 .621.504 1.125 1.125 1.125h9.75c.621 0 1.125-.504 1.125-1.125V16.5a9 9 0 0 0-9-9Z"
        />
      </svg>
      Copy
    </button>
  </div>

  <!-- Toast Notification -->
  {#if showToast}
    <div
      class="absolute bottom-12 left-1/2 transform -translate-x-1/2 px-4 py-2 bg-green-500 text-white rounded-md shadow-lg"
      transition:fade
    >
      Image copied to clipboard!
    </div>
  {/if}
</div>

<style>
  input {
    margin-right: 1rem;
  }
  button {
    padding: 0.5rem 1rem;
    background-color: #40c463;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  button:hover {
    background-color: #30a14e;
  }

  .github-corner:hover .octo-arm {
    animation: octocat-wave 560ms ease-in-out;
  }

  @keyframes octocat-wave {
    0%,
    100% {
      transform: rotate(0);
    }
    20%,
    60% {
      transform: rotate(-25deg);
    }
    40%,
    80% {
      transform: rotate(10deg);
    }
  }

  @media (max-width: 500px) {
    .github-corner:hover .octo-arm {
      animation: none;
    }
    .github-corner .octo-arm {
      animation: octocat-wave 560ms ease-in-out;
    }
  }
</style>

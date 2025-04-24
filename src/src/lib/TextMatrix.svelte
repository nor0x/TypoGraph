<script lang="ts">
  import { onMount } from "svelte";
  import html2canvas from "html2canvas";
  import { fade } from "svelte/transition";


  let text = window.location.pathname.split("/").pop() || "";
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

</script><div class="flex flex-col items-center justify-center p-1 md:p-6 bg-gray-100 min-h-screen">
  <h1
    class="text-[3rem] font-bold text-gray-800"
    style="font-family: 'Fascinate Inline', cursive;">
    TypoGraph
  </h1>
  <h2
    class="text-md text-gray-600 mb-6 mx-auto italic">
    your text as a GitHub contribution graph
  </h2>
  <!-- Matrix Container -->
  <div
    bind:this={matrixRef}
    class="p-4 bg-white rounded-lg shadow-md overflow-hidden mb-6 flex max-w-full content-center"
  >
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
      <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
        <path stroke-linecap="round" stroke-linejoin="round" d="M8.25 7.5V6.108c0-1.135.845-2.098 1.976-2.192.373-.03.748-.057 1.123-.08M15.75 18H18a2.25 2.25 0 0 0 2.25-2.25V6.108c0-1.135-.845-2.098-1.976-2.192a48.424 48.424 0 0 0-1.123-.08M15.75 18.75v-1.875a3.375 3.375 0 0 0-3.375-3.375h-1.5a1.125 1.125 0 0 1-1.125-1.125v-1.5A3.375 3.375 0 0 0 6.375 7.5H5.25m11.9-3.664A2.251 2.251 0 0 0 15 2.25h-1.5a2.251 2.251 0 0 0-2.15 1.586m5.8 0c.065.21.1.433.1.664v.75h-6V4.5c0-.231.035-.454.1-.664M6.75 7.5H4.875c-.621 0-1.125.504-1.125 1.125v12c0 .621.504 1.125 1.125 1.125h9.75c.621 0 1.125-.504 1.125-1.125V16.5a9 9 0 0 0-9-9Z" />
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
</style>
<script>
  import { onMount } from "svelte";

  let title = "";
  let note = "";
  let pages = [];
  let currentPageIndex = 0;

  function saveNote() {
    const storedPageName = pages[currentPageIndex];
    if (storedPageName != title) {
      localStorage.removeItem(storedPageName);
      pages[currentPageIndex] = title;
    }
    localStorage.setItem(title, note);
    localStorage.setItem("pages", JSON.stringify(pages));
  }

  function addPage() {
    pages.push("New Page");
    selectPage(pages.length ? pages.length - 1 : 0);
  }

  function selectPage(index) {
    currentPageIndex = index;
    title = pages[currentPageIndex];
    note = localStorage.getItem(title) || "";
  }

  function deletePage(index) {
    const pageToDelete = pages[index];
    localStorage.removeItem(pageToDelete);
    pages.splice(index, 1);
    localStorage.setItem("pages", JSON.stringify(pages));
    if (pages.length === 0) {
      addPage();
    } else {
      selectPage(index === 0 ? 0 : index - 1);
    }
  }

  onMount(() => {
    const savedPages = localStorage.getItem("pages");
    if (savedPages) {
      pages = JSON.parse(savedPages);
      title = pages[currentPageIndex];
      note = localStorage.getItem(title) || "";
    } else {
      addPage();
    }
  });
</script>

<aside class="fixed top-0 left-0 z-40 w-60">
  <div
    class="bg-gray-300 overflow-y-auto h-screen px-5 py-3 border border-gray-300"
  >
    <ul class="space-y-2">
      {#each pages as page, index}
        <li class="flex justify-between items-center">
          <button
            on:click={() => selectPage(index)}
            class="{index === currentPageIndex
              ? 'bg-gray-400'
              : ''} px-3 py-2 text-black font-bold rounded-lg"
          >
            {page}
          </button>
          <button
            on:click={() => deletePage(index)}
            class="bg-red-500 text-white px-2 py-1 rounded-lg ml-2"
          >
            Delete
          </button>
        </li>
      {/each}
      <li class="text-center">
        <button
          on:click={addPage}
          class="bg-gray-500 px-3 py-2 rounded-lg mt-3 font-medium"
        >
          + Add Page
        </button>
      </li>
    </ul>
  </div>
</aside>

<main class="ml-60 h-auto">
  <div class="p-4 space-y-3">
    <div class="grid grid-cols-2 items-center">
      <h1 class="text-3xl font-bold" contenteditable bind:textContent={title}>
        {title || "New Page"}
      </h1>
      <button
        class="bg-gray-700 text-white ml-auto px-5 py-2 font-bold text-sm hover:bg-gray-900 rounded-lg"
        on:click={saveNote}
      >
        Save
      </button>
    </div>
    <hr />
    <textarea
      class="block w-full border border-gray-400 p-2.5 bg-gray-100 rounded-md"
      bind:value={note}
      id="textArea"
    ></textarea>
  </div>
</main>

<style>
</style>

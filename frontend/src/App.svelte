<script lang="ts">
  let hello = $state<any>(null);

  let globalWs = $state<WebSocket | null>(null);
  let input = $state<string>("");
  let messages = $state<string[]>([]);

  $effect(() => {
    (async () => {
      const ws = new WebSocket("ws://localhost:3000");

      ws.onopen = () => {
        globalWs = ws;
      };

      ws.onmessage = (event) => {
        messages.push(event.data);
      };

      const response = await fetch("/hello");

      if (!response.ok) {
        throw new Error("Failed to fetch");
      }

      const data = await response.text();

      hello = data;
    })();
    return () => {
      if (globalWs) {
        globalWs.close();
      }
    };
  });
</script>

<main>
  <div style="padding: 24px">
    <h1>Ripress + Wynd + Svelte minimal demo</h1>
    <div>
      <h2>/hello</h2>
      <pre>{hello ? JSON.stringify(hello, null, 2) : "Loading..."}</pre>
    </div>
  </div>
  <div>
    <h2>Websocket</h2>
    <input type="text" placeholder="Type something..." bind:value={input} />
    <button onclick={() => globalWs?.send(input)}>Send</button>

    <p>Messages:</p>
    <ul style="list-style: none; padding: 0">
      {#each messages as message}
        <li>{message}</li>
      {/each}
    </ul>
  </div>
</main>

<style>
  .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.svelte:hover {
    filter: drop-shadow(0 0 2em #ff3e00aa);
  }
  .read-the-docs {
    color: #888;
  }
</style>

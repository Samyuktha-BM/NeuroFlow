<script lang="ts">
    import {urls} from "$lib/stores/urls.ts";
    import {classifiers} from "$lib/shared/BinaryClassifiers";
    import WebSocketComponent from "$lib/WebSocketHandler/WebSocketComponent.svelte";
    let message:string = null;
    interface Socket {
      sendMessage: (message: string) => object;
    }
    let socket: Socket;
    let classificationTask = 'RandomForest';
    function handleTrainAndTest(event: Event) {
      const formData = new FormData(event.target as HTMLFormElement);
      const parameters = {};
      for (let [key, value] of formData.entries()) {
        parameters[key] = isNaN(Number(value)) ? value : Number(value);
      }
      const classifier = classificationTask;

      socket.sendMessage(JSON.stringify({parameters, classifier}));
    }
    function handleSelection(classifier) {
        classificationTask = classifier;
    }
</script>

<WebSocketComponent bind:this={socket} url={$urls.scikitlearn_socket} />
<div class="flex h-screen">
    <div class="sidebar bg-base-200 w-1/4">
        <ul class="menu p-4 overflow-y-auto w-full bg-base-100 text-base-content">
            {#each Object.keys(classifiers) as classifier}
                <li>
                    <button
                        on:click={() => handleSelection(classifier)}
                        class="btn btn-ghost w-full justify-start">
                        {classifier.replace(/([A-Z])/g, ' $1').trim()}
                    </button>
                </li>
            {/each}
        </ul>
    </div>
    <div class="w-3/4 p-4">
        <h2 class="text-xl font-bold mb-4">{classificationTask.replace(/([A-Z])/g, ' $1').trim()}</h2>
        <form on:submit|preventDefault={handleTrainAndTest} class="form-control">
            {#each classifiers[classificationTask].params as param}
                <div class="mb-4 form-control">
                    <label class="label">
                        <span class="label-text">{param}</span>
                    </label>
                    <input type="text" class="input input-bordered w-full" name={param}/>
                </div>
            {/each}
            <button type="submit" class="btn btn-primary">Submit</button>
        </form>
        {#if message}
            <div class="alert alert-info mt-4">{message}</div>
        {/if}
    </div>
</div>
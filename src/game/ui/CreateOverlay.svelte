<script>
    import { CellGrid, openLevel } from "@core/cells/grid";
    import { createLevel, mainMenu } from "./uiState";

    let width = 100;
    let height = 100;
    let widthElement = null as any as HTMLInputElement;
    let heightElement = null as any as HTMLInputElement;

    $: if (widthElement) widthElement.focus();
</script>

<style>
    .overlay_container {
        background-color: #363636;
        left: 0;
        position: fixed;
        top: 0;
        z-index: 101;
        width: 100%;
        height: 100%;
    }

    .overlay {
        left: 50%;
        position: fixed;
        top: 50%;
        padding: 20px;
        height: auto;
        transform: translate(-50%, -50%);
        width: auto;
    }

    h2 {
        color: #fff;
        font: 400 35px/40px "Roboto", sans-serif;
        margin: 0;
        padding: 0;
        text-align: center;
    }

    input {
        text-align: center;
        width: 150px;
    }
</style>

{#if $createLevel}
    <div class="overlay_container">
        <div class="overlay">
            <h2>Create Level</h2>
            <div class="space"></div>
            <input type="number" class="big" placeholder="Width" bind:value={width} bind:this={widthElement} on:focus={e => widthElement.select()} />
            <input type="number" class="big" placeholder="Height" bind:value={height} bind:this={heightElement} on:focus={e => heightElement.select()} />
            <div class="space"></div>
            <button class="big" on:click={() => {
                $openLevel = CellGrid.createEmpty(width, height);
                width = 100;
                height = 100;
                $createLevel = false;
                $mainMenu = false;
            }}>Create new level</button>
            <div class="space"></div>
            <button class="center" on:click={() => $createLevel = false}>Back</button>
        </div>
    </div>
{/if}

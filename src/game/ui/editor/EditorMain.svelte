<script>
	import { openLevel } from "@core/cells/grid";
    import CellGridViewer from "./CellGridViewer.svelte";
    import DebugMenu from "./DebugMenu.svelte";
    import GameControls from "./GameControls.svelte";
    import { cursorPosition, screenPosition, selection, selectionContent } from "../uiState";
    import Menu from "./Menu.svelte";

    // $openLevel = CellGrid.loadFromString("V3;1;1;e;;")[1] as CellGrid;
</script>

<style>
    .cell_controller {
        background-color: #2a2a2a;
        display: flex;
        height: 100%;
        width: 100%;
    }
</style>

<div class="cell_controller">
    {#if $openLevel}
        {#key $openLevel}
            <CellGridViewer grid={$openLevel} bind:selectionArea={$selection} bind:selection={$selectionContent} bind:mouseCell={$cursorPosition} bind:center={$screenPosition} />
        {/key}
    {/if}
    <DebugMenu />
    <GameControls />
</div>

<Menu />

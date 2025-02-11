<script context="module">
    import { writable, derived } from "svelte/store";
    import { keys, on } from "../keys";
    import { mainMenu, menuOpen, showControls } from "../uiState";
    import { config } from "@utils/config";
    import { openLevel } from "@core/cells/grid";
    import { currentPack } from "@utils/texturePacks";
    import { Animator } from "@utils/animator";
    import { SlotHandler } from "@core/slot";
    import { Direction } from "@core/coord/direction";
    import { Registry } from "@core/registry";

    let slotHandler = new SlotHandler(Registry.getSlots());
    const currentSlots = slotHandler.slots;

    export const selectedCell = slotHandler.currentCell;
    export const actualRotation = writable(0);
    export const rotation = derived(actualRotation, $r => ($r % 4 + 4) % 4 as Direction);

</script>

<script>
    let show = true;
    on("F1").when(() => !$mainMenu).down(() => (show = !show, showControls.set(show), $menuOpen = false));
    on("q").when(() => !$menuOpen && !$mainMenu).down(() => $actualRotation--);
    on("e").when(() => !$menuOpen && !$mainMenu).down(() => $actualRotation++);
    on("t").when(() => !$menuOpen && !$mainMenu).down(() => (levelPlaying = false, playTimer.stop(), $openLevel?.reset()));

    const playTimer = new Animator(() => {
        $openLevel?.doStep(false);
    });

    $: playTimer.setInterval($config.tickSpeed);

    let levelPlaying = false;
    function toggleLevel() {
        if (levelPlaying) {
            playTimer.stop();
            levelPlaying = false;
        }
        else {
            playTimer.start();
            levelPlaying = true;
        }
    }
    on(" ").when(() => !$menuOpen && !$mainMenu).down(toggleLevel);

    on("tab").down(() => {
        keys.shift ? slotHandler.prev() : slotHandler.next();
    });
    on("<").down(() => {
        slotHandler.loopSlot();
    });
    on("y").when(() => !levelPlaying).down(() => {
        $openLevel?.doStep(false);
    });
</script>

<style>
    .bottom_controls {
        background-color: rgba(65, 65, 65, .7);
        bottom: 0;
        display: flex;
        position: fixed;
        width: 100%;
        z-index: 60;
    }

    .buttons {
        padding: 30px 100px 30px 30px;
    }

    $selection_size: var(--hotbar-size);

    .play {
        background-size: $selection_size, $selection_size;
        background-repeat: no-repeat;
        width: $selection_size;
        height: $selection_size;
    }

    .cells {
        flex: 1;
        display: flex;
        padding: 30px;
    }

    .cell_selection_seperator {
        flex: 1;
    }
    .cell_selection {
        background-repeat: no-repeat;
        background-size: contain;
        display: block;
        image-rendering: pixelated;
        position: relative;
        opacity: .4;
        padding-top: 100%;
        transition: transform .15s, opacity .1s;
        width: $selection_size;
    }
    .selected {
        opacity: 1;
    }
</style>

{#if show}
    <div class="bottom_controls" style="--hotbar-size: {$config.hotbarSize}px;">
        <div class="buttons">
            <div class="play" style="background-image: url({$currentPack.ui[levelPlaying ? "pause" : "play"].url})" on:click={() => toggleLevel()}></div>
        </div>

        <div class="cells">
            {#each $currentSlots as c, i}
                {#if i}
                    <div class="cell_selection_seperator"></div>
                {/if}
                <div>
                    <div class="cell_selection" class:selected={c.isActive} style="
                        background-image: url({$currentPack.textures[c.currentItem.options.textureName].url});
                        transform: rotate({$actualRotation * 90}deg);
                    " on:click={() => {
                        if (c.isActive)
                            slotHandler.loopSlot();
                        else
                            slotHandler.to(i);
                    }}></div>
                </div>
            {/each}
        </div>
    </div>
{/if}

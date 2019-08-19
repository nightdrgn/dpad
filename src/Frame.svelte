<script>
    import Button from './Button.svelte';
    import Pad from './Pad.svelte';
    import History from './History.svelte';
    import Axis from './Axis.svelte';
    const nil = undefined;
    export let frame = nil;

    let axis = [];

    $: {
        if (frame.pad) {
            axis = [];
            for (let i = 0; i < frame.pad.axes.length; i += 2) {
                axis.push([ frame.pad.axes[i], frame.pad.axes[i + 1] ]);
            }
        }
    }
</script>

<style>
.btns {
    padding: 10px;
}

.frame {
    position: absolute;
    top: 0; left: 100px;
}
</style>

{#if frame.pad != nil}

    <div class="btns">
        {#each frame.pad.buttons as btn, idx} 
            <Button {btn} {idx}/> 
        {/each}
    </div>

    <div class="sticks">
        {#each axis as coords, idx} 
            <Axis axis={coords} {idx}/> 
        {/each}
    </div>

    <div class="frame">f {frame.f}</div>

    <Pad state={frame.input} />

    <History state={{ history: frame.history, dpad: frame.dpadHistory, btns: frame.btnHistory }}/>

{/if}
<script>
    const nil = undefined;
    export let btn = nil;
    export let idx = nil;
</script>

<style>
.btn {
    position: relative;
    background: #f9f9f9;
    padding: 10px;
    display: inline-block;
    margin: 5px;
}
.btn .head {
    top: -6px;
    position: absolute;
    left: 5px;
    font-size: 12px;
    background: #000;
    padding: 1px 4px;
    color: #fff;
    font-family: monospace;
}

.pressed {
    background: #d2d2d2;
}
</style>

{#if btn != nil}
    <div class={ btn.pressed ? 'btn pressed' : 'btn' }>
        <div class="head">B{idx}</div> {btn.value.toFixed(2)}
    </div>
{/if}
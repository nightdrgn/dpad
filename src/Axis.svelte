<script>
    const nil = undefined;
    export let axis = nil;
    export let idx = nil;
</script>

<style>
.axis {
    position: relative;
    width: 150px;
    height: 150px;
    border: 1px solid #000;
    float: left;
    margin: 5px;
}

.axis-0 {
    position: absolute;
    top: 123px;
    left: 494px;
    z-index: 999;
    /* position: relative; */
    width: 150px;
    height: 150px;
    /* border: 1px solid #000; */
    float: left;
    margin: 5px;
}

.axis-1 {
    /* display: none; */
    position: absolute;
    top: 50px;
    right: 10px;
    width: 150px;
    height: 150px;
    border: 1px solid #000;
    margin: 5px;
    
}

.axis-0 .head {
    display: none;
}

.axis-1 .point {
    background: red;
}

.point {
    position: absolute;
    width: 10px;
    height: 10px;
    background: #e2ff45;
    margin-top: -5px;
    margin-left: -5px;
}
</style>

<div class={`axix axis-${idx}`}>
    <div class="head">{axis[0] >= 0 ? '+' : ''}{axis[0].toFixed(2)} {axis[1] <= 0 ? '+' : ''}{axis[1].toFixed(2) * -1}</div>
    {#if idx == 1 || axis[0] < -0.1 || axis[0] > +0.1 || axis[1] < -0.1 || axis[1] > +0.1 }
    <div class="point" style={`left: ${(axis[0] + 1) * 100 / 2}%; top: ${(axis[1] + 1) * 100 / 2}%;`}></div>
    {/if}
</div>
<script>
    const nil = undefined;
    export let state = nil;
    
    const stickDirections = [
        '7', '8', '9',
        '4', '5', '6',
        '1', '2', '3'
    ];

    const btns = {
        A: 4, 
        B: 5,
        C: 6,
        D: 7,
        E: 8,
        F: 9
    };

</script>

{#if state != nil}
    <div class="vstick">
        <div class="dirs">
            {#each stickDirections as dir, idx} 
                <img src={ state[dir] > 0 ? `/${dir}a.png` : `/${dir}.png` } alt={dir} width="50" height="50"/>
            {/each}
        </div>
        <div class="btns">
            {#each Object.keys(btns) as btn, idx} 
                <div class={`${state[btn] > 0 ? 'active-' : '' } ${btn} btn`}>
                    <b>{btn}</b>
                </div>
            {/each}
        </div>
    </div>
{/if}

<style>
.vstick {
    position: absolute;
    width: 500px;
    height: 150px;
    /* border: 1px solid #eee; */
    top: 130px;
    left: 500px;
}

.dirs {
    position: absolute;
    top: -5px; left: 0;
    width: 150px; height: 150px;
    /* background: #666; */
}

.dirs img {
    padding: 0;
    margin: 0;
}

.btns {
    position: absolute;
    top: 0;
    left: 150px;
    width: 300px;
    height: 150px;
}

.btn {
    position: relative;
    background: #eee;
    font-size: 32px;
    font-weight: bold;
    padding: 10px;
    display: inline-block;
    width: 1em;
    height: 1em;
    border-radius: 30px;
}

.btn.active- {
    background: #e6df00;
}

.btn b {
    font-weight: bold;
    position: absolute;
    top: 2px;
    
    left: 14px;
}

.A {
    position: absolute;
    top: 47px;
    left: 40px;
}

.B {
    position: absolute;
    top: 7px;
    left: 90px;
}

.B b {
    left: 16px;
}

.C {
    position: absolute;
    top: 47px;
    left: 140px;
}

.D {
    position: absolute;
    top: 87px;
    left: 90px;
}

.D b {
    left: 15px;
}

.E {
    position: absolute;
    top: -17px;
    left: 150px;
}

.E b {
    left: 16px;
}

.F {
    position: absolute;
    top: 110px;
    left: 30px;
}

.F b {
    left: 16px;
}
</style>
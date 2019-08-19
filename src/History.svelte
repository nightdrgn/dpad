<script>
    const nil = undefined;
    export let state = nil;

    let history = [];
    let dpad = [];
    let btns = [];

    $: {
        history = [];
        for (let i = state.history.length - 1; i >= 0; i -= 1) {
            let sig = state.history[i].sig
                .replace('1', '⇙')
                .replace('2', '⇩')
                .replace('3', '⇘')
                .replace('4', '⇦')
                .replace('5', '')
                .replace('6', '⇨')
                .replace('7', '⇖')
                .replace('8', '⇪')
                .replace('9', '⇗')
            history.push({ sig, f: state.history[i].f });
        }

        dpad = [];
        for (let i = state.dpad.length - 1; i >= 0; i -= 1) {
            let sig = state.dpad[i].sig
                .replace('1', '⇙')
                .replace('2', '⇩')
                .replace('3', '⇘')
                .replace('4', '⇦')
                .replace('5', '')
                .replace('6', '⇨')
                .replace('7', '⇖')
                .replace('8', '⇪')
                .replace('9', '⇗')
            dpad.push({ sig, f: state.dpad[i].f });
        }

        btns = [];
        for (let i = state.btns.length - 1; i >= 0; i -= 1) {
            let sig = state.btns[i].sig;
            btns.push({ sig, f: state.btns[i].f });
        }
    }
</script>

<style>
.history {
    background: #eee;
    position: absolute;
    top: 100px;
    left: 10px;
    width: 475px;
    height: 500px;
    overflow-y: scroll;
    font-size: 16px;
}

.history table {
    float: left;
}

.history table tbody tr td:first-child {
    font-weight:bold;
}

.history table {
    width: 150px;
}
</style>

<div class="history">
    
    <table>
        <tbody>
            {#each dpad as entry, idx}
            <tr><td>{entry.sig}</td><td>{entry.f}</td></tr>
            {/each}
        </tbody>
    </table>

    <table>
        <tbody>
            {#each btns as entry, idx}
            <tr><td>{entry.sig}</td><td>{entry.f}</td></tr>
            {/each}
        </tbody>
    </table>

    <table>
        <tbody>
            {#each history as entry, idx}
            <tr><td>{entry.sig}</td><td>{entry.f}</td></tr>
            {/each}
        </tbody>
    </table>

</div>
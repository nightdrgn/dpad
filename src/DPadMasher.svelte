<script>
	import Frame from './Frame.svelte';

	const nil = undefined;

	const dz = 0.45; // deadzone

	// this will just work for both ps4 and xbox one controller
	const defaultMapping = {
		0: 12, // up
		1: 13, // down
		2: 14, // left
		3: 15, // right
		4: 2, // A
		5: 3, // B
		6: 1, // C
		7: 0, // D
		8: 5, // E
		9: 7  // F
	}

	let map = defaultMapping;

	const InputMap = {

		// Positions

		'5': (b, s) => // neutral
			( ! b[2] && ! b[1] && ! b[3] && ! b[0]) // dpad
				&& (s.x > -dz && s.x < +dz && s.y > -dz && s.y < +dz), // stick
		'2': (b, s) => // down
			(b[1] && ! b[2] && ! b[3] && ! b[0]) // dpad
				|| (s.y <= -dz && s.x > -dz && s.x < +dz), // stick
		'4': (b, s) => // back
			( ! b[1] && b[2] && ! b[3] && ! b[0])
				|| (s.x <= -dz && s.y > -dz && s.y < +dz), // stick
		'6': (b, s) => // forward
			( ! b[1] && ! b[2] && b[3] && ! b[0])
				|| (s.x >= +dz && s.y > -dz && s.y < +dz), // stick
		'8': (b, s) => // jump
			( ! b[1] && ! b[2] && ! b[3] && b[0])
				|| (s.y >= +dz && s.x > -dz && s.x < +dz), // stick
		'1': (b, s) => // down back
			( b[1] && b[2] && ! b[3] && ! b[0])
				|| (s.x <= -dz && s.y <= -dz), // stick
		'3': (b, s) => // down forward
			( b[1] && ! b[2] && b[3] && ! b[0])
				|| (s.x >= +dz && s.y <= -dz), // stick
		'7': (b, s) => // jump back
			( ! b[1] && b[2] && ! b[3] && b[0])
				|| (s.x <= -dz && s.y >= +dz), // stick
		'9': (b, s) => // jump forward
			( ! b[1] && ! b[2] && b[3] && b[0])
				|| (s.x >= +dz && s.y >= +dz), // stick
		
		// Buttons

		'A': (b, s) => b[4] > 0,
		'B': (b, s) => b[5] > 0,
		'C': (b, s) => b[6] > 0,
		'D': (b, s) => b[7] > 0,
		'E': (b, s) => b[8] > 0,
		'F': (b, s) => b[9] > 0

	};

	const PageState = {
		Waiting: 0,
		Active: 1,
		Disconected: 2
	}

	let state = PageState.Waiting;
	
	let controller;
	let controllerIdx;

	let status;
	$: if (state == PageState.Waiting) {
		status = 'No Controller Active. Press a button on controller while page is focused.';
	}
	else if (state == PageState.Active) {
		status = 'Active';
	}
	else if (state == PageState.Disconected) {
		status = 'Disconnected Pad. Uh, might need to refresh.';
	}

	window.addEventListener('gamepadconnected', event => {
		console.log("A gamepad connected:");
    	console.log(event.gamepad);
		state = PageState.Active;
		controller = event.gamepad.id;
		controllerIdx = event.gamepad.index;
	});

	window.addEventListener('gamepaddisconnected', event => {
		console.log("A gamepad disconnected:");
    	console.log(event.gamepad);
		state = PageState.Disconected;
		controller = nil;
	});

	//////////////////////////////////////////////////////////////////////////////////////

	$: frame = { pad: nil };
	let f = 0;
	
	let history = [];
	let dpadHistory = [];
	let btnHistory = [];
	
	function gameloop() {
		if (state != PageState.Active) {
			frame = { pad: nil };
			return;
		}

		f += 1;

		let gamepads = navigator.getGamepads();
		let pad = gamepads[controllerIdx];

		let stick = {
			x: pad.axes[0],
			y: pad.axes[1] * -1
		};

		let input = {};
		let btns = [];
		for (let i = 0; i < pad.buttons.length; i += 1) {
			btns[i] = pad.buttons[i].pressed ? pad.buttons[i].value : 0.0;
		}

		let sig = '';
		let dpadSig = '';
		let btnSig = '';

		let mappedBtns = [];
		for (let i = 0; i <= 9; i += 1) {
			mappedBtns.push(btns[map[i]]);
		}

		for (let key of Object.keys(InputMap)) {
			input[key] = InputMap[key](mappedBtns, stick) ? 1.0 : 0.0;
			
			if (input[key] > 0.0) {
				sig += key;

				if (/[0-9]/.test(key)) {
					dpadSig += key;
				}
				else {
					btnSig += key;
				}
			}
		}

		if (history.length == 0) {
			history.push({ sig: '5', f: 0, at: f });
		}

		if (dpadHistory.length == 0) {
			dpadHistory.push({ sig: '5', f: 0, at: f });
		}

		if (btnHistory.length == 0) {
			btnHistory.push({ sig: '', f: 0, at: f });
		}

		let last = history[history.length - 1];
		if (last.sig == sig) {
			if (last.f < 999) {
				last.f += 1;
			}
		}
		else {
			history.push({ sig, f: 1, at: f });
		}

		let lastDpad = dpadHistory[dpadHistory.length - 1];
		if (lastDpad.sig == dpadSig) {
			if (lastDpad.f < 999) {
				lastDpad.f += 1;
			}
		}
		else {
			dpadHistory.push({ sig: dpadSig, f: 1, at: f });
		}

		let lastBtn = btnHistory[btnHistory.length - 1];
		if (lastBtn.sig == btnSig) {
			if (lastBtn.f < 999) {
				lastBtn.f += 1;
			}
		}
		else {
			btnHistory.push({ sig: btnSig, f: 1, at: f });
		}

		frame = { f, pad, input, history, dpadHistory, btnHistory };
	}

	//////////////////////////////////////////////////////////////////////////////////////

	let fpsInterval, now, then, elapsed;

	function frames() {
		// request another frame
		requestAnimationFrame(frames);

		// calc elapsed time since last loop
		now = Date.now();
		elapsed = now - then;

		// if enough time has elapsed, draw the next frame
		if (elapsed > fpsInterval) {
			// get ready for next frame by setting then=now, but also adjust for your
			// specified fpsInterval not being a multiple of RAF's interval (16.7ms)
			then = now - (elapsed % fpsInterval);

			gameloop();
		}
	}

	function start(fps) {
		fpsInterval = 1000 / fps;
		then = Date.now();
		frames();
	}

	start(60); // aim for 60fps

</script>

<style>
h1 {
    position: absolute;
    top: 0;
    left: 50%;
    font-size: 12px;
    line-height: 1em;
}

.status {
	font-size: 12px;
    position: absolute;
    top: 0;
    left: 0;
    padding: 5px;
    line-height: 1em;
}

.device {
	font-size: 12px;
    position: absolute;
    top: 0;
    right: 0;
    padding: 5px;
    line-height: 1em;
}

.frame {
	padding-top: 25px;
}

</style>

<h1>dPad Masher</h1>

<div class="status"><b>Status</b>: {status}</div>
<div class="device">{controller ? controller : 'no active pad' }</div>

<div class="frame">
	<Frame {frame} />
</div>
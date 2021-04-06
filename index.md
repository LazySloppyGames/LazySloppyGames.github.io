<!DOCTYPE html>
<html lang="en-US">

<head>

<title>Dice Puzzle</title>

<style>
	#wrapper { 
		display: flex; 
		justify-content: center; /* center horizontally */ 
		align-items: center; /* center vertically */ 
	} 
</style>

</head>

<body>

<div id="wrapper"> 
	<canvas id="gameBoard" width="700" height="700">
		Your browser does not support the HTML canvas tag.
	</canvas>
</div> 

<script>

	// Wait for the page to load before starting.
	window.addEventListener('load', mainProgram, false);
	
	function mainProgram() {

		// Set up canvas for 2D graphics
		var c = document.getElementById("gameBoard");
		const ctx = c.getContext("2d", { alpha: false });

		// Constants
		const fps = 60;
		const interval = 1000 / fps;
		const width = gameBoard.width;
		const height = gameBoard.height;
		const diceWidth = gameBoard.width - 100;
		const diceHeight = gameBoard.height - 100;
		const dieCount = 4;
		const boxSize = diceWidth / (dieCount + 1);
		const boxSpacing = boxSize / (dieCount + 1);
		const dotSize = boxSize / 9;
		const dotSpacing = dotSize * 1.5;
		const winColor = "#FF0000";
		const normalColor = "#FFFFFF";
		
		// Dice Faces
		const diceFace = [[
			[0, 0, 0],
			[0, 1, 0],
			[0, 0, 0]
		],
		[
			[0, 0, 1],
			[0, 0, 0],
			[1, 0, 0]
		],
		[
			[0, 0, 1],
			[0, 1, 0],
			[1, 0, 0]
		],
		[
			[1, 0, 1],
			[0, 0, 0],
			[1, 0, 1]
		],
		[
			[1, 0, 1],
			[0, 1, 0],
			[1, 0, 1]
		],
		[
			[1, 0, 1],
			[1, 0, 1],
			[1, 0, 1]
		]];
		
		// Variables
		var dice = [];
		var mouseX = -1;
		var mouseY = -1;
		var dieDirection = "none";
		var dieElement = -1;
		var diceMoveDirection = 0;
		var winner = false;
		
		randomizeDice();
		gameBoard.addEventListener("mousedown", mousePos, false);
		mainLoop();
		
		function mousePos(event) {
			mouseX = event.x - c.offsetLeft;
			mouseY = event.y - c.offsetTop;
			dieDirection = "none";
		  dieElement = -1;
		  diceMoveDirection = 0;
			checkButtons();
			shiftDice();
		}
		
		function shiftDice() {
		
			if (dieDirection == "column") {
				for (var i = 0; i < dieCount; i++) {
					dice[dieElement][i] += diceMoveDirection;
					if (dice[dieElement][i] < 0)
						dice[dieElement][i] = 5;
					else if (dice[dieElement][i] > 5)
						dice[dieElement][i] = 0;
				}
			}
			else if (dieDirection == "row") {
				for (var i = 0; i < dieCount; i++) {
					dice[i][dieElement] += diceMoveDirection;
					if (dice[i][dieElement] < 0)
						dice[i][dieElement] = 5;
					else if (dice[i][dieElement] > 5)
						dice[i][dieElement] = 0;
				}
			}
			else if (dieDirection == "diagonal") {
				for (var i = 0; i < dieCount; i++) {
					if (dieElement == 0) {
						dice[i][i] += diceMoveDirection;
						if (dice[i][i] < 0)
							dice[i][i] = 5;
						else if (dice[i][i] > 5)
							dice[i][i] = 0;
					}
					else {
						dice[dieCount-1-i][i] += diceMoveDirection;
						if (dice[dieCount-1-i][i] < 0)
							dice[dieCount-1-i][i] = 5;
						else if (dice[dieCount-1-i][i] > 5)
							dice[dieCount-1-i][i] = 0;
					}
				}
			}
		
		}
		
		function checkButtons() {
		  // Check columns & rows
			for (var i = 0; i < dieCount; i++) {
				var posX = 50 + boxSpacing * (i + 1) + boxSize * i;
				
				// Top Arrows
				var posY = 0;
				if (mouseX > posX && mouseX < posX + boxSize &&
						mouseY > posY && mouseY < posY + 50 + boxSpacing) {
					dieDirection = "column";
					dieElement = i;
					diceMoveDirection = 1;
					return;
				}
				
				// Bottom Arrows
				posY = height - 50 - boxSpacing;
				if (mouseX > posX && mouseX < posX + boxSize &&
						mouseY > posY && mouseY < posY + 50 + boxSpacing) {
					dieDirection = "column";
					dieElement = i;
					diceMoveDirection = -1;
					return;
				}
				
				var posY = 50 + boxSpacing * (i + 1) + boxSize * i;
				
				// Left Arrows
				var posX = 0;
				if (mouseX > posX && mouseX < posX + 50 + boxSpacing &&
						mouseY > posY && mouseY < posY + boxSize) {
					dieDirection = "row";
					dieElement = i;
					diceMoveDirection = 1;
					return;
				}
				
				// Right Arrows
				posX = width - 50 - boxSpacing;
				if (mouseX > posX && mouseX < posX + 50 + boxSpacing &&
						mouseY > posY && mouseY < posY + boxSize) {
					dieDirection = "row";
					dieElement = i;
					diceMoveDirection = -1;
					return;
				}
				
			}

			// Top-Left
			if (mouseX > 0 && mouseX < 50 + boxSpacing && mouseY > 0 && mouseY < 50 + boxSpacing) {
					dieDirection = "diagonal";
					dieElement = 0;
					diceMoveDirection = 1;
					return;
			}
			
			// Top-Right
			if (mouseX > width - 50 - boxSpacing && mouseX < width && mouseY > 0 && mouseY < 50 + boxSpacing) {
					dieDirection = "diagonal";
					dieElement = 1;
					diceMoveDirection = 1;
					return;
			}
			
			// Bottom-Left
			if (mouseX > 0 && mouseX < 50 + boxSpacing && mouseY > height - 50 - boxSpacing && mouseY < height) {
					dieDirection = "diagonal";
					dieElement = 1;
					diceMoveDirection = -1;
					return;
			}
			
			// Bottom-Right
			if (mouseX > width - 50 - boxSpacing && mouseX < width && mouseY > height - 50 - boxSpacing && mouseY < height) {
					dieDirection = "diagonal";
					dieElement = 0;
					diceMoveDirection = -1;
					return;
			}
			
			
			window.setTimeout(mainLoop,interval);
			
		}
		
		function randomizeDice() {
		  // Wipe out previous die values.
			dice = [];
		
			// Set all dice to 6
		  for (var k = 0; k < dieCount; k++) {
				var dieRow = [];
				for (var l = 0; l < dieCount; l++) {
					dieRow.push(5);
				}
				dice.push(dieRow);
			}
			
			diceMoveDirection = 1;
			const linesToChooseFrom = dieCount * 2 + 2;
			const linesToChange = linesToChooseFrom * 10;
			for (var i = 0; i < linesToChange; i++) {
				var pickedLine = Math.floor(Math.random() * linesToChooseFrom);
				if (pickedLine < dieCount) {
					dieDirection = "row";
					dieElement = pickedLine;
				}
				else if (pickedLine < dieCount * 2) {
					dieDirection = "column";
					dieElement = pickedLine - dieCount;
				}
				else {
					dieDirection = "diagonal";
					dieElement = pickedLine - dieCount * 2;
				}
				shiftDice();
			}
		}
		
		function drawDiceBoxes() {
			ctx.strokeStyle = "#00FF00";
			
			for (var i = 0; i < dieCount; i++) {
				for (var j = 0; j < dieCount; j++) {
				  const diceX = 50 + boxSpacing * (i + 1) + boxSize * i;
					const diceY = 50 + boxSpacing * (j + 1) + boxSize * j;
					ctx.beginPath();
					ctx.rect(diceX, diceY, boxSize, boxSize);
					ctx.stroke();
					if (winner)
						ctx.fillStyle = winColor;
					else
						ctx.fillStyle = normalColor;
					for (var m = 0; m < 3; m++) {
						for (var n = 0; n < 3; n++) {
							if (diceFace[dice[i][j]][n][m] == 1) {
								const dotX = dotSpacing * (m + 1) + dotSize * m + diceX;
								const dotY = dotSpacing * (n + 1) + dotSize * n + diceY;
								ctx.fillRect(dotX, dotY, dotSize, dotSize);
							}
						}
					}
				}
			}
		}
		
		function drawArrows() {
		
			ctx.fillStyle = "#336633";
			var arrowTipX;
			var arrowTipY;
			const arrowSize = boxSize * 0.2;
			
			for (var count = 0; count < dieCount; count++) {

				// Top Arrow (Points Down)
				arrowTipX = 50 + boxSpacing * (count + 1) + boxSize * count + boxSize / 2.0;
				arrowTipY = 50 + boxSpacing - boxSpacing;
				ctx.beginPath();
				ctx.moveTo(arrowTipX, arrowTipY);
				ctx.lineTo(arrowTipX + arrowSize, arrowTipY - arrowSize);
				ctx.lineTo(arrowTipX - arrowSize, arrowTipY - arrowSize);
				ctx.fill();
				
				// Bottom Arrow (Points Up)
				arrowTipY = 50 + diceHeight;
				ctx.beginPath();
				ctx.moveTo(arrowTipX, arrowTipY);
				ctx.lineTo(arrowTipX + arrowSize, arrowTipY + arrowSize);
				ctx.lineTo(arrowTipX - arrowSize, arrowTipY + arrowSize);
				ctx.fill();
				
				// Left Arrow (Points Right)
				arrowTipX = 50 + boxSpacing - boxSpacing;
				arrowTipY = 50 + boxSpacing * (count + 1) + boxSize * count + boxSize / 2.0;
				ctx.beginPath();
				ctx.moveTo(arrowTipX, arrowTipY);
				ctx.lineTo(arrowTipX - arrowSize, arrowTipY - arrowSize);
				ctx.lineTo(arrowTipX - arrowSize, arrowTipY + arrowSize);
				ctx.fill();
				
				// Right Arrow (Points Left)
				arrowTipX = 50 + diceWidth;
				ctx.beginPath();
				ctx.moveTo(arrowTipX, arrowTipY);
				ctx.lineTo(arrowTipX + arrowSize, arrowTipY - arrowSize);
				ctx.lineTo(arrowTipX + arrowSize, arrowTipY + arrowSize);
				ctx.fill();
			}
			
			const diagonalSize = arrowSize * Math.SQRT2;
			
			// Draw diagonal arrows
			arrowTipX = 50 + boxSpacing;
			arrowTipY = 50 + boxSpacing;
			ctx.beginPath();
			ctx.moveTo(arrowTipX, arrowTipY);
			ctx.lineTo(arrowTipX - diagonalSize, arrowTipY);
			ctx.lineTo(arrowTipX, arrowTipY - diagonalSize);
			ctx.fill();
			
			arrowTipY = height - 50 - boxSpacing;
			ctx.beginPath();
			ctx.moveTo(arrowTipX, arrowTipY);
			ctx.lineTo(arrowTipX - diagonalSize, arrowTipY);
			ctx.lineTo(arrowTipX, arrowTipY + diagonalSize);
			ctx.fill();
			
			arrowTipX = width - 50 - boxSpacing;
			arrowTipY = 50 + boxSpacing;
			ctx.beginPath();
			ctx.moveTo(arrowTipX, arrowTipY);
			ctx.lineTo(arrowTipX + diagonalSize, arrowTipY);
			ctx.lineTo(arrowTipX, arrowTipY - diagonalSize);
			ctx.fill();
			
			arrowTipX = width - 50 - boxSpacing;
			arrowTipY = height - 50 - boxSpacing;
			ctx.beginPath();
			ctx.moveTo(arrowTipX, arrowTipY);
			ctx.lineTo(arrowTipX + diagonalSize, arrowTipY);
			ctx.lineTo(arrowTipX, arrowTipY + diagonalSize);
			ctx.fill();
			
		}
		
		function checkForWin() {
			var winningValue = dice[0][0];
			for (var i = 0; i < dieCount; i++) {
				for (var j = 0; j < dieCount; j++) {
					if (dice[i][j] != winningValue) {
						winner = false;
						return;
					}
				}
			}
			winner = true;
		}
		
		function eraseScreen() {
			ctx.fillStyle = "#000000";
			ctx.fillRect(0, 0, width, height);
		}

		function logic() {
			checkForWin();
		}
		
		function draw() {
			eraseScreen();
			drawDiceBoxes();
			drawArrows();
		}
		
		function mainLoop() {
		  logic();
		  draw();

		  window.setTimeout(mainLoop, interval);
		}
		
	}
	
</script>

</body>


</html>

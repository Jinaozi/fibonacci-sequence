<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fibonacci Spiral Visualization</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            padding: 20px;
            background-color: #CFD7D6;
        }
        input, select {
            padding: 10px;
            font-size: 16px;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            margin-top: 10px;
        }
        canvas {
            margin-top: 20px;
            border: 1px solid #16438B;
        }
        .result {
            margin-top: 20px;
            font-size: 18px;
        }
    </style>
</head>
<body>
    <h1>Fibonacci Sequence and Spiral Generator</h1>
    <div>
        <label for="inputValue">Enter value:</label>
        <input type="number" id="inputValue" min="1" required>        
        <select id="actionSelect">
            <option value="spiral">Generate Spiral</option>
            <option value="sequence">Calculate Fibonacci Sequence</option>
        </select>
        <button id="actionBtn">Submit</button>
    </div>
    <div class="result" id="output"></div>
    <canvas id="spiralCanvas" width="600" height="600"></canvas>  
    <script>
        var FibonacciGenerator = function() {
            var thisFibonacci = this;
            // Start with 0, 1 instead of the real sequence
            thisFibonacci.array = [0, 1];
            thisFibonacci.getDiscrete = function(n) {
                // If the Fibonacci number is not in the array, calculate it
                while (n >= thisFibonacci.array.length) {
                    var length = thisFibonacci.array.length;
                    var nextFibonacci = thisFibonacci.array[length - 1] + thisFibonacci.array[length - 2];
                    thisFibonacci.array.push(nextFibonacci);
                }
                return thisFibonacci.array[n];
            };
            thisFibonacci.getNumber = function(n) {
                var floor = Math.floor(n);
                var ceil = Math.ceil(n);
                if (Math.floor(n) === n) {
                    return thisFibonacci.getDiscrete(n);
                }
                var a = Math.pow(n - floor, 1.15);
                var fibFloor = thisFibonacci.getDiscrete(floor);
                var fibCeil = thisFibonacci.getDiscrete(ceil);   
                return fibFloor + a * (fibCeil - fibFloor);
            };
            return thisFibonacci;
        };
        var getSpiral = function(pA, pB, maxRadius) {
            var precision = 50; // Lines to draw in each quarter turn
            var stepB = 4; // Steps to get to point B
            var angleToPointB = getAngle(pA, pB); // Angle between pA and pB
            var distToPointB = getDistance(pA, pB); // Distance between pA and pB
            var fibonacci = new FibonacciGenerator();
            // Calculate scale so that the last point of the curve is at distance to pB
            var radiusB = fibonacci.getNumber(stepB);
            var scale = distToPointB / radiusB;
            // Calculate angle offset so that last point of the curve is at angle to pB
            var angleOffset = angleToPointB - stepB * Math.PI / 2;
            var path = [];
            var i, step, radius, angle;
            // Start at the center
            i = step = radius = angle = 0;
            // Continue drawing until reaching maximum radius
            while (radius * scale <= maxRadius) {
                path.push({
                    x: scale * radius * Math.cos(angle + angleOffset) + pA.x,
                    y: scale * radius * Math.sin(angle + angleOffset) + pA.y
                });
                i++; // Next point
                step = i / precision; // Quarter turns at point    
                radius = fibonacci.getNumber(step); // Radius of Fibonacci spiral
                angle = step * Math.PI / 2; // Radians at point
            }              
            return path;
        };
        function getAngle(pA, pB) {
            return Math.atan2(pB.y - pA.y, pB.x - pA.x);
        }
        function getDistance(pA, pB) {
            return Math.sqrt(Math.pow(pB.x - pA.x, 2) + Math.pow(pB.y - pA.y, 2));
        }
        function drawSpiral(maxRadius) {
            const canvas = document.getElementById('spiralCanvas');
            const ctx = canvas.getContext('2d');
            ctx.clearRect(0, 0, canvas.width, canvas.height); // Clear canvas
            const centerPoint = { x: canvas.width / 2, y: canvas.height / 2 };
            const endPoint = { x: canvas.width / 2 + maxRadius * Math.cos(Math.PI / 4), y: canvas.height / 2 + maxRadius * Math.sin(Math.PI / 4) }; // Point B
            const spiralPath = getSpiral(centerPoint, endPoint, maxRadius);
            ctx.beginPath();          
            spiralPath.forEach((point, index) => {
                if (index === 0) {
                    ctx.moveTo(point.x, point.y);
                } else {
                    ctx.lineTo(point.x, point.y);
                }
            });
            ctx.strokeStyle = 'blue';
            ctx.lineWidth = 2;
            ctx.stroke();
        }
        function calculateFibonacci(n) {
            let fib_sequence = [0, 1];    
            for (let i = 2; i < n; i++) {
                let next_term = fib_sequence[i-1] + fib_sequence[i-2];
                fib_sequence.push(next_term);
            }        
            return fib_sequence.slice(0, n);
        }
        document.getElementById("actionBtn").onclick = function() {
          const inputValue = parseFloat(document.getElementById("inputValue").value);
          const actionType = document.getElementById("actionSelect").value;
          if (actionType === "spiral") {
              drawSpiral(inputValue);
              document.getElementById("output").innerText = ""; // Clear previous output
          } else if (actionType === "sequence") {
              const resultSequence = calculateFibonacci(inputValue);
              document.getElementById("output").innerText =
                  `The first ${inputValue} terms of the Fibonacci sequence are: ${resultSequence.join(", ")}`;
          }
      };
    </script>
</body>
</html>

<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mô Phỏng Định Luật Boyle - Vật Lý 12</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #f4f7f6;
            text-align: center;
            margin: 0;
            padding: 20px;
        }
        h2 {
            color: #2c3e50;
        }
        .container {
            display: inline-block;
            background: #fff;
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
        }
        canvas {
            background: #fff;
            border: 2px solid #333;
            border-radius: 8px;
            margin-top: 10px;
        }
        .controls {
            margin-top: 15px;
        }
        input[type=range] {
            width: 350px;
            cursor: pointer;
        }
        .info {
            font-size: 18px;
            margin-top: 15px;
            font-weight: bold;
            color: #2980b9;
        }
        .description {
            font-size: 14px;
            color: #555;
            margin-top: 10px;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
            line-height: 1.5;
        }
    </style>
</head>
<body>

<div class="container">
    <h2>Thí Nghiệm Vật Lý 12: Khảo Sát Định Luật Boyle (Đẳng Nhiệt)</h2>
    <p class="description">Kéo thanh trượt để thay đổi thể tích ($V$) của xi-lanh. Quan sát sự thay đổi áp suất ($p$) và chuyển động của các phân tử khí để thấy tích số $p \times V$ luôn không đổi.</p>
    
    <canvas id="simCanvas" width="600" height="350"></canvas>
    
    <div class="controls">
        <label for="volumeSlider"><strong>Thay đổi Thể tích ($V$):</strong></label><br>
        <input type="range" id="volumeSlider" min="80" max="250" value="160">
    </div>
    
    <div class="info" id="infoText"></div>
</div>

<script>
    const canvas = document.getElementById('simCanvas');
    const ctx = canvas.getContext('2d');
    const slider = document.getElementById('volumeSlider');
    const infoText = document.getElementById('infoText');

    // Hằng số khí cho đẳng nhiệt: p * V = const
    const CONST_K = 48000;

    // Tạo các hạt khí mô phỏng bên trong xi-lanh
    const particles = [];
    for(let i = 0; i < 50; i++) {
        particles.push({
            x: Math.random() * 180 + 210,
            y: Math.random() * 220 + 60,
            vx: (Math.random() - 0.5) * 6,
            vy: (Math.random() - 0.5) * 6
        });
    }

    function draw() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        // Vị trí và kích thước xi-lanh cố định
        const cylX = 200;
        const cylY = 50;
        const cylWidth = 200;
        const maxHeight = 260; // Thể tích max tương ứng chiều cao này

        // Thể tích V tỉ lệ thuận với chiều cao của cột khí bên trong xi-lanh
        let V = parseInt(slider.value);
        let p = (CONST_K / V).toFixed(1); // Áp suất tính theo định luật Boyle

        // 1. Vẽ thân xi-lanh
        ctx.strokeStyle = '#2c3e50';
        ctx.lineWidth = 5;
        ctx.strokeRect(cylX, cylY, cylWidth, maxHeight);

        // 2. Vẽ piston di động (nắp đậy phía trên cột khí)
        ctx.fillStyle = '#7f8c8d';
        ctx.fillRect(cylX, cylY, cylWidth, V); // V thay đổi từ 80 đến 250 tương ứng piston di chuyển lên xuống
        
        // Tay cầm piston
        ctx.fillStyle = '#34495e';
        ctx.fillRect(cylX + 80, cylY - 20, 40, 20);

        // 3. Vẽ và cập nhật các phân tử khí bên trong phần thể tích khí (từ đáy piston đến đáy xi-lanh)
        ctx.fillStyle = '#e74c3c';
        particles.forEach(pt => {
            // Tốc độ hạt tăng nhẹ khi không gian hẹp hơn (mật độ va chạm lớn hơn)
            let speedFactor = 200 / V;
            pt.x += pt.vx * speedFactor;
            pt.y += pt.vy * speedFactor;

            // Va chạm thành xi-lanh (giới hạn từ đáy piston đến đáy cố định ở y = 310)
            let topLimit = cylY + V;
            let bottomLimit = cylY + maxHeight;

            if (pt.x < cylX + 5 || pt.x > cylX + cylWidth - 5) pt.vx *= -1;
            if (pt.y < topLimit + 5 || pt.y > bottomLimit - 5) pt.vy *= -1;

            ctx.beginPath();
            ctx.arc(pt.x, pt.y, 3.5, 0, Math.PI * 2);
            ctx.fill();
        });

        // 4. Hiển thị thông số trực quan
        infoText.innerHTML = `Thể tích ($V$): ${V} cm³ &nbsp;|&nbsp; Áp suất ($p$): ${p} kPa &nbsp;|&nbsp; $p \\times V$ = ${(p * V).toFixed(0)}`;

        requestAnimationFrame(draw);
    }

    draw();
</script>

</body>
</html>

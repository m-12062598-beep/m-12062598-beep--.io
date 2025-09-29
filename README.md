<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>迷宫小游戏</title>
    <style>
        body { background: #f8f8f8; font-family: Arial, sans-serif; }
        #mazeCanvas { background: #fff; display: block; margin: 30px auto; border: 2px solid #333; }
        #info { text-align: center; margin-top: 10px; }
        .btn { padding: 8px 16px; margin: 5px; font-size: 16px; }
    </style>
</head>
<body>
    <canvas id="mazeCanvas" width="600" height="600"></canvas>
    <div id="info"></div>
    <div style="text-align:center;">
        <button class="btn" onclick="generateMaze()">新迷宫</button>
        <button class="btn" onclick="resetPlayer()">重置位置</button>
    </div>
    <script>
const ROWS = 20, COLS = 20, CELL = 30;
let maze = [], visited = [], stack = [], player = {x:0, y:0}, end = {x:COLS-1, y:ROWS-1};
const canvas = document.getElementById('mazeCanvas');
const ctx = canvas.getContext('2d');
const infoDiv = document.getElementById('info');

function generateMaze() {
    maze = [];
    visited = [];
    for(let y=0;y<ROWS;y++){
        maze[y]=[]; visited[y]=[];
        for(let x=0;x<COLS;x++){
            maze[y][x]={top:true,right:true,bottom:true,left:true};
            visited[y][x]=false;
        }
    }
    stack = [{x:0,y:0}];
    visited[0][0]=true;
    while(stack.length){
        let curr = stack[stack.length-1];
        let dirs = [];
        if(curr.y>0 && !visited[curr.y-1][curr.x]) dirs.push('top');
        if(curr.x<COLS-1 && !visited[curr.y][curr.x+1]) dirs.push('right');
        if(curr.y<ROWS-1 && !visited[curr.y+1][curr.x]) dirs.push('bottom');
        if(curr.x>0 && !visited[curr.y][curr.x-1]) dirs.push('left');
        if(dirs.length){
            let dir = dirs[Math.floor(Math.random()*dirs.length)];
            let nx=curr.x,ny=curr.y;
            if(dir==='top'){ maze[curr.y][curr.x].top=false; ny--; maze[ny][nx].bottom=false; }
            if(dir==='right'){ maze[curr.y][curr.x].right=false; nx++; maze[ny][nx].left=false; }
            if(dir==='bottom'){ maze[curr.y][curr.x].bottom=false; ny++; maze[ny][nx].top=false; }
            if(dir==='left'){ maze[curr.y][curr.x].left=false; nx--; maze[ny][nx].right=false; }
            visited[ny][nx]=true;
            stack.push({x:nx,y:ny});
        }else{
            stack.pop();
        }
    }
    player = {x:0, y:0};
    end = {x:COLS-1, y:ROWS-1};
    draw();
    infoDiv.innerHTML = '用方向键移动到右下角终点！';
}

function draw(){
    ctx.clearRect(0,0,canvas.width,canvas.height);
    ctx.strokeStyle = '#000';
    for(let y=0;y<ROWS;y++){
        for(let x=0;x<COLS;x++){
            let cell = maze[y][x];
            let px = x*CELL, py = y*CELL;
            if(cell.top) { ctx.beginPath(); ctx.moveTo(px,py); ctx.lineTo(px+CELL,py); ctx.stroke(); }
            if(cell.right) { ctx.beginPath(); ctx.moveTo(px+CELL,py); ctx.lineTo(px+CELL,py+CELL); ctx.stroke(); }
            if(cell.bottom) { ctx.beginPath(); ctx.moveTo(px+CELL,py+CELL); ctx.lineTo(px,py+CELL); ctx.stroke(); }
            if(cell.left) { ctx.beginPath(); ctx.moveTo(px,py+CELL); ctx.lineTo(px,py); ctx.stroke(); }
        }
    }
    // 终点
    ctx.fillStyle = '#ffd700';
    ctx.fillRect(end.x*CELL+4,end.y*CELL+4,CELL-8,CELL-8);
    // 玩家
    ctx.fillStyle = '#00f';
    ctx.beginPath();
    ctx.arc(player.x*CELL+CELL/2,player.y*CELL+CELL/2,CELL/3,0,2*Math.PI);
    ctx.fill();
}

function resetPlayer(){
    player = {x:0, y:0};
    draw();
    infoDiv.innerHTML = '用方向键移动到右下角终点！';
}

window.addEventListener('keydown',function(e){
    let moved = false;
    if(e.code==='ArrowUp' && !maze[player.y][player.x].top){ player.y--; moved=true; }
    if(e.code==='ArrowDown' && !maze[player.y][player.x].bottom){ player.y++; moved=true; }
    if(e.code==='ArrowLeft' && !maze[player.y][player.x].left){ player.x--; moved=true; }
    if(e.code==='ArrowRight' && !maze[player.y][player.x].right){ player.x++; moved=true; }
    if(moved){
        draw();
        if(player.x===end.x && player.y===end.y){
            infoDiv.innerHTML = '<span style="color:green;font-size:24px;">恭喜你到达终点！</span>';
        }
    }
});

generateMaze();
    </script>
</body>
</html>

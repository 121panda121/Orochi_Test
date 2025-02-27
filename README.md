
记得去设置-网站设置-弹出式窗口重定向（打开拦截网站弹出）（由于任务颇多，会有很多网页弹出导致卡顿）   
```bash
// 使用 setInterval 来分别处理 get 和 claim 按钮的点击
function autoClickButtons() {
    // 点击所有 "get" 按钮，每5秒执行一次
    setInterval(() => {
        const getButtons = Array.from(document.querySelectorAll('button')).filter(btn => 
            btn.textContent.toLowerCase().includes('get')
        );
        
        if (getButtons.length > 0) {
            getButtons.forEach((btn, index) => {
                setTimeout(() => {
                    btn.click();
                    console.log(`Clicked "get" button ${index + 1}`);
                }, index * 100); // 轻微延迟避免同时点击
            });
        }
    }, 5000); // 每5秒检查并点击一次

    // 点击所有 "claim" 按钮，每10秒执行一次
    setInterval(() => {
        const claimButtons = Array.from(document.querySelectorAll('button')).filter(btn => 
            btn.textContent.toLowerCase().includes('claim')
        );
        
        if (claimButtons.length > 0) {
            claimButtons.forEach((btn, index) => {
                setTimeout(() => {
                    btn.click();
                    console.log(`Clicked "claim" button ${index + 1}`);
                }, index * 100); // 轻微延迟避免同时点击
            });
        }
    }, 10000); // 每10秒检查并点击一次

    // 每15秒执行一次完整的 get 和 claim 后滚动
    setInterval(() => {
        // 在完成一次 get 和 claim 后向下滚动
        window.scrollBy({
            top: window.innerHeight, // 滚动一个视窗高度
            behavior: 'smooth' // 平滑滚动
        });
        console.log("Scrolled down");
    }, 15000); // 每15秒滚动一次，确保 get 和 claim 都执行过
}

// 启动脚本
autoClickButtons();

// 如果需要停止脚本，可以在控制台调用此函数
function stopAutoClick() {
    clearInterval();
    console.log("Auto click stopped");
}
```

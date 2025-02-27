控制台使用，最近网站流量太大，会出现网络不佳点击失败的情况。   
记得去设置-网站设置-弹出式窗口重定向（打开拦截网站弹出）（由于任务颇多，会有很多网页弹出导致卡顿）   
```bash
// 自动点击函数
function autoClickButtons() {
    // 存储所有 "get" 按钮的位置
    let getButtonPositions = [];
    let getClickRounds = 0; // 记录 "get" 点击的轮数

    // 处理 "get" 按钮：逐个点击并记录位置，完成后重复点击记录的位置
    function clickNextGetButton() {
        const getButtons = Array.from(document.querySelectorAll('button')).filter(btn => 
            btn.textContent.toLowerCase().includes('get')
        );

        // 如果是第一次运行，记录所有 "get" 按钮的位置
        if (getButtonPositions.length === 0 && getButtons.length > 0) {
            getButtonPositions = getButtons.map(btn => ({
                element: btn,
                boundingRect: btn.getBoundingClientRect()
            }));
            console.log(`Recorded ${getButtonPositions.length} "get" button positions`);
        }

        let currentIndex = 0;

        function clickAndWait() {
            if (currentIndex < getButtonPositions.length) {
                const { element } = getButtonPositions[currentIndex];
                element.click();
                console.log(`Clicked "get" button ${currentIndex + 1} of ${getButtonPositions.length} at recorded position`);
                currentIndex++;
                setTimeout(clickAndWait, 5000); // 休息5秒后点击下一个
            } else if (getButtonPositions.length > 0) {
                getClickRounds++; // 完成一轮 "get" 点击
                console.log(`Completed "get" round ${getClickRounds}, restarting from recorded positions...`);
                setTimeout(clickNextGetButton, 5000); // 完成后休息5秒再从头开始
            } else {
                console.log("No 'get' buttons found, checking again in 5 seconds...");
                setTimeout(clickNextGetButton, 5000); // 如果没有按钮，5秒后重试
            }
        }

        if (getButtonPositions.length > 0) {
            clickAndWait();
        } else {
            console.log("No 'get' buttons found initially, checking again in 5 seconds...");
            setTimeout(clickNextGetButton, 5000); // 如果没有按钮，5秒后重试
        }
    }

    // 处理 "claim" 按钮：在 "get" 点击两轮后开始，每15秒检查并点击符合条件的按钮
    function startClaimClicks() {
        if (getClickRounds >= 2) {
            console.log("Starting 'claim' button clicks after 2 rounds of 'get'...");
            setInterval(() => {
                const allButtons = Array.from(document.querySelectorAll('button'));
                const claimButtons = allButtons.filter(btn => 
                    btn.textContent.toLowerCase().includes('claim')
                );

                claimButtons.forEach((btn, index) => {
                    // 获取按钮的垂直位置
                    const btnRect = btn.getBoundingClientRect();
                    const btnTop = btnRect.top;
                    const btnBottom = btnRect.bottom;

                    // 检查同行高度的后续元素
                    const siblings = Array.from(btn.parentElement.children);
                    const btnIndex = siblings.indexOf(btn);
                    let hasGet = false;

                    for (let i = btnIndex + 1; i < siblings.length; i++) {
                        const nextElement = siblings[i];
                        const nextRect = nextElement.getBoundingClientRect();
                        const nextTop = nextRect.top;
                        const nextBottom = nextRect.bottom;

                        // 判断是否在同一行（垂直位置重叠）
                        if (nextTop < btnBottom && nextBottom > btnTop) {
                            const nextText = nextElement.textContent.toLowerCase();
                            if (nextText.includes('get')) {
                                hasGet = true;
                                break;
                            }
                        }
                    }

                    if (hasGet) {
                        console.log(`Skipped "claim" button ${index + 1} (followed by "get")`);
                    } else {
                        btn.click();
                        console.log(`Clicked "claim" button ${index + 1}`);
                    }
                });

                if (claimButtons.length === 0) {
                    console.log("No 'claim' buttons found");
                }
            }, 15000); // 每15秒检查并点击一次
        } else {
            // 如果 "get" 还没完成两轮，继续等待
            setTimeout(startClaimClicks, 1000); // 每秒检查一次
        }
    }

    // 启动 "get" 按钮的点击
    clickNextGetButton();

    // 启动 "claim" 检查
    startClaimClicks();
}

// 启动脚本
autoClickButtons();

// 如果需要停止脚本，可以在控制台调用此函数
function stopAutoClick() {
    clearInterval();
    console.log("Auto click stopped");
}
```   
                                                Twitter：@longyueting

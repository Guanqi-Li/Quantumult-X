// 米游社获取cookie&原神、星穹铁道、绝区零签到

// 米游社获取cookie
// ===================== 配置区域 =====================
const TARGET_URL = "https://public-data-api.mihoyo.com/device-fp/api/getExtList?platform=1&app_name=bbs_cn";
const STORAGE_KEY = "mihoyo_cookie";

// 主函数
if (typeof $request !== 'undefined') {
    handleRequestCapture();
} else {
    $notify("脚本错误", "请在Quantumult X中作为HTTP请求脚本使用", "请添加到[rewrite_local]");
}

function handleRequestCapture() {
    // 确保匹配目标URL
    if ($request.url !== TARGET_URL) {
        $done({});
        return;
    }
    
    // 获取请求头中的Cookie
    const cookie = $request.headers['Cookie'] || $request.headers['cookie'];
    
    if (cookie) {
        // 保存到持久化存储
        $prefs.setValueForKey(cookie, STORAGE_KEY);
        
        // 发送通知
        $notify("米哈游Cookie捕获成功", "已保存到持久化存储", "键名: " + STORAGE_KEY);
        
        // 输出日志
        console.log(`捕获的Cookie: ${cookie}`);
        console.log(`已保存到键: ${STORAGE_KEY}`);
    } else {
        $notify("Cookie捕获失败", "目标网站", "请求头中未找到Cookie");
        console.log("请求头: " + JSON.stringify($request.headers));
    }
    
    // 继续原始请求（不修改）
    $done({});
}


// 原神、星穹铁道、绝区零签到
// ===================== 配置区域 =====================
const COOKIE_KEY = "mihoyo_cookie"; // 持久化存储的Cookie键名
const DEVICE_FP = "38d8053efbbf2"; // 设备指纹（固定值）
const DEVICE_ID = "0DC757C7-DB72-46E1-9320-849325A7CBE8"; // 设备ID（固定值）

// ===================== 工具函数 =====================
/**
 * 创建米哈游API请求
 * @param {string} game - 游戏标识（zzz/hk4e/hkrpg）
 * @param {string} uid - 游戏UID
 * @param {string} actId - 活动ID
 * @param {string} region - 服务器区域
 * @param {string} dsToken - DS校验令牌
 * @returns {object} 请求配置对象
 */
function createMihoyoRequest(game, uid, actId, region, dsToken) {
    const commonHeaders = {
        'Accept-Encoding': 'gzip, deflate, br',
        'Host': 'api-takumi.mihoyo.com',
        'x-rpc-device_model': 'iPhone17,2',
        'Origin': 'https://act.mihoyo.com',
        'Sec-Fetch-Dest': 'empty',
        'Connection': 'keep-alive',
        'x-rpc-device_name': 'iPhone',
        'Accept-Language': 'zh-CN,zh-Hans;q=0.9',
        'Sec-Fetch-Site': 'same-site',
        'User-Agent': 'Mozilla/5.0 (iPhone; CPU iPhone OS 18_2_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) miHoYoBBS/2.90.0',
        'Referer': 'https://act.mihoyo.com/',
        'Content-Type': 'application/json;charset=utf-8',
        'x-rpc-device_fp': DEVICE_FP,
        'Accept': 'application/json, text/plain, */*',
        'x-rpc-app_version': '2.90.0',
        'x-rpc-client_type': '5',
        'x-rpc-device_id': DEVICE_ID,
        'Sec-Fetch-Mode': 'cors',
        'x-rpc-platform': '1',
        'x-rpc-signgame': game,
        'DS': dsToken
    };
    
    // 从持久化存储读取Cookie
    const savedCookie = $prefs.valueForKey(COOKIE_KEY);
    if (!savedCookie) {
        $notify("❌ 签到失败", "Cookie未配置", "请先运行Cookie捕获脚本");
        return null;
    }
    
    return {
        url: `https://api-takumi.mihoyo.com/event/luna/${game}/sign`,
        method: 'POST',
        headers: {
            ...commonHeaders,
            'Cookie': savedCookie
        },
        body: JSON.stringify({
            "act_id": actId,
            "region": region,
            "uid": uid,
            "lang": "zh-cn"
        })
    };
}

/**
 * 执行签到请求
 * @param {object} requestConfig - 请求配置
 * @param {string} gameName - 游戏名称（用于通知）
 * @returns {Promise<string>} 签到结果描述
 */
async function performSign(requestConfig, gameName) {
    if (!requestConfig) return `${gameName}：配置错误`;
    
    try {
        const response = await $task.fetch(requestConfig);
        
        // 检查HTTP状态码
        if (response.statusCode !== 200) {
            return `${gameName}：请求失败 (HTTP ${response.statusCode})`;
        }
        
        // 解析响应内容
        const result = JSON.parse(response.body);
        
        // 检查API返回码
        if (result.retcode !== 0) {
            return `${gameName}：${result.message || 'API错误'} (${result.retcode})`;
        }
        
        // 检查是否重复签到
        if (result.message === "旅行者，你已经签到过了") {
            return `${gameName}：今日已签到`;
        }
        
        return `${gameName}：签到成功`;
    } catch (error) {
        return `${gameName}：请求异常 - ${error.error || error}`;
    }
}

// ===================== 主执行逻辑 =====================
(async () => {
    // 创建三个游戏的请求配置
    const zzzRequest = createMihoyoRequest(
        "zzz", 
        "27475587", 
        "e202406242138391", 
        "prod_gf_cn", 
        "1750217200,UPIWTT,44f613f530e7c39025b061cdc6515409"
    );
    
    const hk4eRequest = createMihoyoRequest(
        "hk4e", 
        "221477215", 
        "e202311201442471", 
        "cn_gf01", 
        "1750359973,ttnGEI,4e3014f6ed6dabf87e4651f2e2231488"
    );
    
    const hkrpgRequest = createMihoyoRequest(
        "hkrpg", 
        "102475881", 
        "e202304121516551", 
        "prod_gf_cn", 
        "1750359946,05ZjjF,3398e68aef6400d97a7a7f80bff680ec"
    );
    
    // 并行执行所有签到请求
    const results = await Promise.all([
        performSign(zzzRequest, "绝区零"),
        performSign(hk4eRequest, "原神"),
        performSign(hkrpgRequest, "星穹铁道")
    ]);
    
    // 生成通知内容
    const notificationContent = results.join("\n");
    
    // 检查是否有失败的情况
    const allSuccess = results.every(r => r.includes("成功") || r.includes("已签到"));
    
    // 发送通知
    $notify(
        allSuccess ? "✅ 米哈游签到完成" : "⚠️ 米哈游签到结果", 
        "", 
        notificationContent
    );
    
    $done();
})();

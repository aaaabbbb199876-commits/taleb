<?php

// ضع هنا التوكن الخاص ببوتك
$botToken = "8873085840:AAHGGy-ITGKIVgKunedftDQ4Swzre0JTNXU";
$website = "https://api.telegram.org/bot" . $botToken;

// استقبال البيانات القادمة من تيليجرام
$content = file_get_contents("php://input");
$update = json_decode($content, TRUE);

if (!$update) {
    exit;
}

$chatId = $update["message"]["chat"]["id"];
$text = $update["message"]["text"];

if ($text == "/start") {
    sendMessage($chatId, "أهلاً بك! أرسل لي أي نص وسأقوم بتحويله إلى مقطع صوتي.");
} else {
    // إخبار المستخدم أن العمل جارٍ
    sendMessage($chatId, "جاري معالجة النص وتحويله لصوت... انتظر قليلاً.");
    
    // تحويل النص إلى صوت وإرساله
    sendVoice($chatId, $text);
}

// دالة إرسال الرسائل النصية
function sendMessage($chatId, $message) {
    global $website;
    $url = $website . "/sendMessage?chat_id=" . $chatId . "&text=" . urlencode($message);
    file_get_contents($url);
}

// دالة توليد الصوت وإرساله كمقطع صوتي
function sendVoice($chatId, $text) {
    global $website;

    // تنظيف النص وتجهيز رابط Google TTS
    // نستخدم اللغة العربية 'ar'
    $text = urlencode($text);
    $audioUrl = "https://translate.google.com/translate_tts?ie=UTF-8&client=tw-ob&q={$text}&tl=ar";

    // إرسال المقطع الصوتي عبر رابط مباشر
    $url = $website . "/sendVoice?chat_id=" . $chatId . "&voice=" . urlencode($audioUrl);
    
    $response = file_get_contents($url);
    return $response;
}

?>

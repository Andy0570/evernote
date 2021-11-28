AVSpeechSynthesizer 及其相关类可以将文本转换为语音输出。

使用示例：
需要导入框架：`#import <AVFoundation/AVFoundation.h>`
```objective-c
    // 语音合成器
    AVSpeechSynthesizer *speechSynthesizer = [[AVSpeechSynthesizer alloc] init];
    speechSynthesizer.delegate = self;
    // 需要转换的文本
    AVSpeechUtterance *utterance = [AVSpeechUtterance speechUtteranceWithString:@"人们敢于登上险峻的高山，只为追寻惊艳；人们乐于眺望辽阔的大海，只为饱览奇观；但人们也会在街上擦肩而过，却对彼此一无所感。倘若每个个体都是一场神迹，那为何所有人都对这神迹视而不见？"];
    // 设置语言类别
    AVSpeechSynthesisVoice *voice = [AVSpeechSynthesisVoice voiceWithLanguage:@"zh-CN"];
    utterance.voice = voice;
    // 设置播放语速
    utterance.rate = 0.4;
    // 设置声调，属性值介于 0.5(低音调)~2.0(高音调) 之间。
    utterance.pitchMultiplier = 1.0;
    // 设置朗读语句停顿延迟
    utterance.postUtteranceDelay = 0.1;
    // 语音合成器生成音频
    [speechSynthesizer speakUtterance:utterance];
```
### AVSpeechSynthesizerDelegate 可以监听的代理方法
```objective-c
#pragma mark - AVSpeechSynthesizerDelegate

- (void)speechSynthesizer:(AVSpeechSynthesizer *)synthesizer didStartSpeechUtterance:(AVSpeechUtterance *)utterance {
    NSLog(@"%s",__func__);
}

- (void)speechSynthesizer:(AVSpeechSynthesizer *)synthesizer didFinishSpeechUtterance:(AVSpeechUtterance *)utterance {
    NSLog(@"%s",__func__);
}

- (void)speechSynthesizer:(AVSpeechSynthesizer *)synthesizer didPauseSpeechUtterance:(AVSpeechUtterance *)utterance {
    NSLog(@"%s",__func__);
}

- (void)speechSynthesizer:(AVSpeechSynthesizer *)synthesizer didContinueSpeechUtterance:(AVSpeechUtterance *)utterance {
    NSLog(@"%s",__func__);
}

- (void)speechSynthesizer:(AVSpeechSynthesizer *)synthesizer didCancelSpeechUtterance:(AVSpeechUtterance *)utterance {
    NSLog(@"%s",__func__);
}

- (void)speechSynthesizer:(AVSpeechSynthesizer *)synthesizer willSpeakRangeOfSpeechString:(NSRange)characterRange utterance:(AVSpeechUtterance *)utterance {
    NSLog(@"%s",__func__);
}
```


### 语言类别
```
Language: ar-SA, Name: Maged, Quality: Default [com.apple.ttsbundle.Maged-compact]
Language: cs-CZ, Name: Zuzana, Quality: Default [com.apple.ttsbundle.Zuzana-compact]
Language: da-DK, Name: Sara, Quality: Default [com.apple.ttsbundle.Sara-compact]
Language: de-DE, Name: Anna, Quality: Default [com.apple.ttsbundle.Anna-compact]
Language: de-DE, Name: Helena, Quality: Default [com.apple.ttsbundle.siri_female_de-DE_compact]
Language: de-DE, Name: Martin, Quality: Default [com.apple.ttsbundle.siri_male_de-DE_compact]
Language: el-GR, Name: Melina, Quality: Default [com.apple.ttsbundle.Melina-compact]
Language: en-AU, Name: Catherine, Quality: Default [com.apple.ttsbundle.siri_female_en-AU_compact]
Language: en-AU, Name: Gordon, Quality: Default [com.apple.ttsbundle.siri_male_en-AU_compact]
Language: en-AU, Name: Karen, Quality: Default [com.apple.ttsbundle.Karen-compact]
Language: en-GB, Name: Arthur, Quality: Default [com.apple.ttsbundle.siri_male_en-GB_compact]
Language: en-GB, Name: Daniel, Quality: Default [com.apple.ttsbundle.Daniel-compact]
Language: en-GB, Name: Martha, Quality: Default [com.apple.ttsbundle.siri_female_en-GB_compact]
Language: en-IE, Name: Moira, Quality: Default [com.apple.ttsbundle.Moira-compact]
Language: en-US, Name: Aaron, Quality: Default [com.apple.ttsbundle.siri_male_en-US_compact]
Language: en-US, Name: Fred, Quality: Default [com.apple.speech.synthesis.voice.Fred]
Language: en-US, Name: Nicky, Quality: Default [com.apple.ttsbundle.siri_female_en-US_compact]
Language: en-US, Name: Samantha, Quality: Default [com.apple.ttsbundle.Samantha-compact]
Language: en-ZA, Name: Tessa, Quality: Default [com.apple.ttsbundle.Tessa-compact]
Language: es-ES, Name: Monica, Quality: Default [com.apple.ttsbundle.Monica-compact]
Language: es-MX, Name: Paulina, Quality: Default [com.apple.ttsbundle.Paulina-compact]
Language: fi-FI, Name: Satu, Quality: Default [com.apple.ttsbundle.Satu-compact]
Language: fr-CA, Name: Amelie, Quality: Default [com.apple.ttsbundle.Amelie-compact]
Language: fr-FR, Name: Daniel, Quality: Default [com.apple.ttsbundle.siri_male_fr-FR_compact]
Language: fr-FR, Name: Marie, Quality: Default [com.apple.ttsbundle.siri_female_fr-FR_compact]
Language: fr-FR, Name: Thomas, Quality: Default [com.apple.ttsbundle.Thomas-compact]
Language: he-IL, Name: Carmit, Quality: Default [com.apple.ttsbundle.Carmit-compact]
Language: hi-IN, Name: Lekha, Quality: Default [com.apple.ttsbundle.Lekha-compact]
Language: hu-HU, Name: Mariska, Quality: Default [com.apple.ttsbundle.Mariska-compact]
Language: id-ID, Name: Damayanti, Quality: Default [com.apple.ttsbundle.Damayanti-compact]
Language: it-IT, Name: Alice, Quality: Default [com.apple.ttsbundle.Alice-compact]
Language: ja-JP, Name: Hattori, Quality: Default [com.apple.ttsbundle.siri_male_ja-JP_compact]
Language: ja-JP, Name: Kyoko, Quality: Default [com.apple.ttsbundle.Kyoko-compact]
Language: ja-JP, Name: O-ren, Quality: Default [com.apple.ttsbundle.siri_female_ja-JP_compact]
Language: ko-KR, Name: Yuna, Quality: Default [com.apple.ttsbundle.Yuna-compact]
Language: nl-BE, Name: Ellen, Quality: Default [com.apple.ttsbundle.Ellen-compact]
Language: nl-NL, Name: Xander, Quality: Default [com.apple.ttsbundle.Xander-compact]
Language: no-NO, Name: Nora, Quality: Default [com.apple.ttsbundle.Nora-compact]
Language: pl-PL, Name: Zosia, Quality: Default [com.apple.ttsbundle.Zosia-compact]
Language: pt-BR, Name: Luciana, Quality: Default [com.apple.ttsbundle.Luciana-compact]
Language: pt-PT, Name: Joana, Quality: Default [com.apple.ttsbundle.Joana-compact]
Language: ro-RO, Name: Ioana, Quality: Default [com.apple.ttsbundle.Ioana-compact]
Language: ru-RU, Name: Milena, Quality: Default [com.apple.ttsbundle.Milena-compact]
Language: sk-SK, Name: Laura, Quality: Default [com.apple.ttsbundle.Laura-compact]
Language: sv-SE, Name: Alva, Quality: Default [com.apple.ttsbundle.Alva-compact]
Language: th-TH, Name: Kanya, Quality: Default [com.apple.ttsbundle.Kanya-compact]
Language: tr-TR, Name: Yelda, Quality: Default [com.apple.ttsbundle.Yelda-compact]
Language: zh-CN, Name: Yu-shu (Enhanced), Quality: Enhanced [com.apple.ttsbundle.siri_female_zh-CN_premium]
Language: zh-CN, Name: Li-mu, Quality: Default [com.apple.ttsbundle.siri_male_zh-CN_compact]
Language: zh-CN, Name: Ting-Ting, Quality: Default [com.apple.ttsbundle.Ting-Ting-compact]
Language: zh-CN, Name: Yu-shu, Quality: Default [com.apple.ttsbundle.siri_female_zh-CN_compact]
Language: zh-HK, Name: Sin-Ji, Quality: Default [com.apple.ttsbundle.Sin-Ji-compact]
Language: zh-TW, Name: Mei-Jia, Quality: Default [com.apple.ttsbundle.Mei-Jia-compact]
```

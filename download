export default async function handler(req, res) {
  // CORS
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('Access-Control-Allow-Methods', 'POST, OPTIONS');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type');
  if (req.method === 'OPTIONS') return res.status(200).end();
  if (req.method !== 'POST') return res.status(405).json({ error: 'Method not allowed' });

  const apiKey = process.env.ANTHROPIC_API_KEY;
  if (!apiKey) return res.status(500).json({ error: 'ANTHROPIC_API_KEY not configured' });

  try {
    const { topic, answer } = req.body;
    if (!topic || !answer) return res.status(400).json({ error: 'topic and answer required' });

    const criteria = [
      '意外性（15点満点）', '裏切り力（15点満点）', '言葉選び（12点満点）',
      'テンポ・リズム（8点満点）', '映像喚起力（12点満点）', '共感・あるある（13点満点）',
      'お題との整合性（10点満点）', '簡潔さ・切れ味（15点満点）'
    ].map(c => `- ${c}`).join('\n');

    const prompt = `あなたはプロの大喜利審査員です。以下のお題と回答を8つの観点から多面的に採点してください。

【お題】${topic}
【回答】${answer}

以下の8項目でそれぞれ採点してください（合計100点満点）：
${criteria}

各項目の意味：
- 意外性(15): 予想外の角度からの回答か。ありきたりでないか
- 裏切り力(15): 読み手の期待を良い意味で裏切っているか。オチの衝撃度
- 言葉選び(12): 単語のチョイスが巧みか。固有名詞・オノマトペの使い方
- テンポ・リズム(8): 文章のリズム感。読み上げた時の心地よさ。間の取り方
- 映像喚起力(12): 聞いた人がその場面をありありと想像できるか
- 共感・あるある(13): 万人に通じる「わかるー！」要素があるか
- お題との整合性(10): お題に対して的確に答えているか。脱線していないか
- 簡潔さ・切れ味(15): 無駄な言葉がなく研ぎ澄まされているか。短い中にキレがあるか

採点は厳しめに。本当に面白い回答のみ合計80点以上。平凡は40-55点、少し面白い程度は55-70点。

各項目にはその項目に対する一言コメント(20字以内)もつけてください。

また全体の総評アドバイスを300文字程度で書いてください。良い点、改善点、上達のヒントを具体的に。

必ず以下のJSON形式のみで回答してください（他のテキストは一切不要）：
{"total":合計点数,"surprise":点数,"twist":点数,"wordplay":点数,"rhythm":点数,"imagery":点数,"relatability":点数,"coherence":点数,"brevity":点数,"c_surprise":"コメント","c_twist":"コメント","c_wordplay":"コメント","c_rhythm":"コメント","c_imagery":"コメント","c_relatability":"コメント","c_coherence":"コメント","c_brevity":"コメント","advice":"総評アドバイス全文"}`;

    const apiRes = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'x-api-key': apiKey,
        'anthropic-version': '2023-06-01'
      },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 1500,
        messages: [{ role: 'user', content: prompt }]
      })
    });

    if (!apiRes.ok) {
      const err = await apiRes.text();
      console.error('Anthropic API error:', err);
      return res.status(apiRes.status).json({ error: 'API call failed' });
    }

    const data = await apiRes.json();
    const text = data.content.map(c => c.text || '').join('');
    const clean = text.replace(/```json|```/g, '').trim();
    const result = JSON.parse(clean);

    return res.status(200).json(result);
  } catch (e) {
    console.error('Score handler error:', e);
    return res.status(500).json({ error: 'Internal server error' });
  }
}

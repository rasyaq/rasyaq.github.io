
## The Evolution of Transformers into GPT - How AI is Rewriting Itself


Transformers have taken the world by storm. First introduced in 2017, these novel neural network architectures rapidly became the dominant approach across natural language processing and computer vision. 

At the heart of Transformers are self-attention mechanisms that allow modeling long-range dependencies in sequences. For a sequence of inputs $x1, x2, ..., x_n$, self-attention calculates attention scores between each pair:

$$ Attention(x_i, x_j) = softmax(x_i WQ (x_j WK)^T) $$

where WQ and WK are learned projection matrices that map the inputs to a queries and keys vector space. This gives a matrix of attention scores used to aggregate the values, enabling modeling context from the entire sequence.

For the readers to understand the gist of Transformers on technical and intuitionn level, please read my previous notes: https://rasyaq.github.io/2022/07/10/Understanding-Transformers.html

In 2018, researchers at OpenAI took Transformers to the next level with the Generative Pre-trained Transformer (GPT). GPT was pre-trained on a massive text corpus to learn universal language representations that could then be fine-tuned for downstream tasks.
For example, the implementation shows the key aspects in code:

    import torch
    import torch.nn as nn

    # Attention and multi-headed attention
    class Attention(nn.Module):
        def __init__(self, emb_dim):
            super().__init__() 
            self.query = nn.Linear(emb_dim, emb_dim)
            self.key = nn.Linear(emb_dim, emb_dim)
            self.value = nn.Linear(emb_dim, emb_dim)
            self.sqrt_emb_dim = emb_dim ** 0.5
        
        def forward(self, x):
            queries = self.query(x)
            keys = self.key(x)
            values = self.value(x)
            weights = torch.bmm(queries, keys.transpose(1,2)) / self.sqrt_emb_dim 
            attn_outputs = torch.bmm(weights, values)
            return attn_outputs
        
    class MultiHeadAttention(nn.Module):
        def __init__(self, emb_dim, num_heads):
            super().__init__()
            self.heads = nn.ModuleList([Attention(emb_dim) for _ in range(num_heads)])
            self.linear = nn.Linear(emb_dim, emb_dim)
        
        def forward(self, x):
            attn_outputs = torch.cat([h(x) for h in self.heads], dim=-1)
            aggregated_outputs = self.linear(attn_outputs)
            return aggregated_outputs
    
    # Model
    class GPT(nn.Module):
        def __init__(self, vocab_size, emb_dim, n_layers):
            super().__init__()
            self.embeddings = nn.Embedding(vocab_size, emb_dim)
            self.layers = nn.ModuleList([nn.Linear(emb_dim, emb_dim) for _ in range(n_layers)]) 
            self.lm_head = nn.Linear(emb_dim, vocab_size)
        
        def forward(self, x):
            h = self.embeddings(x)
            for layer in self.layers:
                h = layer(h)
            logits = self.lm_head(h)
            return logits

    # Training
    model = GPT(vocab_size=5000, emb_dim=64, n_layers=2) 

    for inputs, targets in dataloader:
        logits = model(inputs)
        loss = nn.functional.cross_entropy(logits.reshape(-1, 5000), targets.reshape(-1))
        loss.backward()
        optimizer.step()
        optimizer.zero_grad()

GPT pioneered the idea of self-supervised pre-training in NLP. By pre-training on unlabeled text, GPT learns relationships between words and how language flows naturally. This general linguistic knowledge can then be transferred and adapted for specific applications.

Successive GPT models have followed a trend of scaling up size and training data. GPT-2 stunned the AI community by generating coherent paragraphs of text given just a sentence prompt. GPT-3 took it even further, using 175 billion parameters trained on unfathomable amounts of text data scraped from the Internet.

GPT-3 displays eerily human-like writing abilities, even though it has no understanding of the content it generates. Its capabilities and potential societal impacts have sparked heated debate. 

Meanwhile, the GPT approach keeps spreading to new domains like computer vision and multimodal applications. Models like DALL-E 2 can generate realistic images from text captions. The line between AI creativity and mimicking human output continues to blur.

The journey from Transformers to GPT illustrates an acceleration in AI capabilities fueled bycombining generalizable architectures with vast computational resources and data. 
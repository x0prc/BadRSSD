# BadRSSD
Based on the implementations in paper titled : BadRSSD: Backdoor Attacks on Regularized Self-Supervised Diffusion Models

## Citation
`Jiayao Wang, et al. "BadRSSD: Backdoor Attacks on Regularized Self-Supervised Diffusion Models." arXiv preprint arXiv:2603.01019 (2026).`

## Background
The research introduces BadRSSD, a pioneering backdoor attack targeting the internal representation layer of self-supervised diffusion models, which differs fundamentally from traditional attacks that only manipulate final generative outputs. The authors first develop a Regularized Self-Supervised Diffusion (RSSD) baseline that uses representation dispersion regularization to ensure feature space uniformity, creating a robust framework for learning visual semantics in a PCA-latent space. However, this structured semantic environment introduces a novel "attack surface" where BadRSSD can hijack the latent identity of poisoned samples through PCA-space alignment, effectively forcing the model to associate a specific trigger with a malicious target representation. By utilizing a conditional triple-loss framework that optimizes across PCA alignment, pixel reconstruction, and feature distribution, the attack achieves high specificity upon trigger activation while remaining completely stealthy during normal operations. This study highlights a critical vulnerability in unified generative-representational models, demonstrating that their sophisticated internal structures can be exploited to bypass existing defenses that focus primarily on output anomalies or neuron activation patterns.

## Diagram
```mermaid
graph TD
    subgraph "1. Input & Pre-processing"
        A[Input Image] --> B{Data Type?}
        B -- Clean --> C[Clean Samples x]
        B -- Poisoned --> D[Poisoned Samples x_p + Trigger]
    end

    subgraph "2. PCA Latent Space & Attack Logic"
        C --> E[PCA Projection V]
        D --> E
        E --> F["PCA Coefficients (Z₀)"]
        
        subgraph "Backdoor Hijacking"
            T[Target Image Z₀ᵀ] -.-> G{Alignment}
            F -.-> G
            G -- Aligned Path --> H["Target-Aligned Latent (Z₀ᵃ)"]
        end
    end

    subgraph "3. Diffusion Process (DiT Architecture)"
        F --> I["Forward Noise (Zₜ)"]
        H --> I
        I --> J["Reconstruct Patches (xₜ = V · Zₜ)"]
        J --> K[VAE Encoder]
        K --> L["DiT Transformer (Denoising)"]
        L --> M[VAE Decoder]
        M --> N["Generated Output (x̂₀)"]
    end

    subgraph "4. Optimization & Objectives"
        L -- "Feature Uniformity" --> O["L_disp (Dispersion Loss)"]
        N -- "Target Fidelity" --> P["L_img_rec (Reconstruction)"]
        L -- "Trajectory Control" --> Q["L_PCA_TR (Alignment Loss)"]
        
        O --> R((Final Model))
        P --> R
        Q --> R
    end

    %% Styling
    style D fill:#f96,stroke:#333,stroke-width:2px
    style G fill:#f96,stroke:#333,stroke-dasharray: 5 5
    style T fill:#f96,stroke:#333
    style H fill:#f96,stroke:#333
    style Q fill:#f96,stroke:#333
    style R fill:#fff,stroke:#333,stroke-width:4px
```

---
layout: fragment
title: 模型校准, ECE代码以及公式
tags: [calibration, uncertenty]
description: 模型校准相关内容
keywords: ML, calibration
mermaid: false
sequence: false
flow: false
mathjax: true 
mindmap: false
mindmap2: false
---

##  模型校准

关于模型校准方面的内容，比如ECE

## 代码

代码如下：

```python

def visual_calibration(true, pre, var1, var2, var):
    mu_values = pre
    sigma_sets = [var1, var2, var]
    confidence_levels = [0.0, 0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]
    calibration_dicts = [{level: 0 for level in confidence_levels} for _ in range(3)]
    total_elements = len(mu_values)
    for i, sigma_values in enumerate(sigma_sets):
        for level in confidence_levels:
            lower_bounds, upper_bounds = norm.interval(level, loc=mu_values, scale=sigma_values)
            is_in_interval = (true >= lower_bounds) & (true <= upper_bounds)
            calibration_dicts[i][level] = np.sum(is_in_interval) / total_elements

    fig, axs = plt.subplots(1, 3, figsize=(15, 4))
    pos = 0.825
    for i, calibration_dict in enumerate(calibration_dicts):
        experimental_ci=[calibration_dict[level] for level in confidence_levels]
        axs[i].plot(confidence_levels, experimental_ci, marker='o')
        ece = np.sum(np.abs(np.array(confidence_levels) - np.array(experimental_ci)) * confidence_levels)
        # 45-degree line represents the ideal calibration curve
        axs[i].plot([0, 1], [0, 1], linestyle='--', color='orange')
        axs[i].set_xlabel('Confidence Level')
        if i == 0:
            axs[i].set_ylabel('Proportion of Observed Elements')
        axs[i].fill_between(confidence_levels, confidence_levels, experimental_ci, alpha=0.2)
        subplots_bbox = axs[i].get_position()
        subplots_right_bottom = (subplots_bbox.x1, subplots_bbox.y0)
        axs[i].text(subplots_right_bottom[0]-0.06, subplots_right_bottom[1]+0.05, f"ECE={ece:.2f}",
                transform=fig.transFigure,
                bbox=dict(facecolor='orange',alpha=0.2, edgecolor='gray', boxstyle='round,pad=0.3'))
        axs[i].grid(True)  # Add grid to the subplot
    fig.suptitle('Calibration Curves for Three Different Standard Deviation Sets')
    fig.savefig("./PDF_IMAGES/calibration_ece.pdf", bbox_inches='tight')
    plt.show()

```

## 公式

ECE的公式如下：

$$\begin{align}
ECE & = \sum_{i=1}^k p(i)|acc(i)-conf(i)|\\
p(i) & = \sum_{i=1}^k conf(i)i\\
j'k\end{align} $$

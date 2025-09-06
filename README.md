![DeepFusion Banner](https://user-images.githubusercontent.com/your-username/deepfusion-product-intelligence/assets/banner.png)

# DeepFusion: Multi-Modal Product Intelligence  

## Executive Summary  
E-commerce catalogs are growing fast, and so is the cost of keeping them clean and accurate. Product categorization is still handled by a mix of manual work and basic pipelines that often get it wrong. A mislabeled product doesn’t just sit in the wrong shelf—it means weaker search results, poor recommendations, and frustrated customers.  

DeepFusion tackles this challenge by combining two powerful signals: **product images** and **metadata** (like category labels and attributes). Instead of treating them separately, the model fuses both, giving it a fuller understanding of each product.  

Our experiments show:  
- **Metadata-only model:** ~81% accuracy  
- **Image-only model:** ~85.7% accuracy  
- **Fusion model (images + metadata):** ~86.4% accuracy  

At first glance, the lift from fusion seems modest. But at catalog scale, it means **thousands of products categorized correctly that would otherwise slip through**. For a retailer, that translates directly to fewer manual fixes, better product discovery, smarter recommendations, and lower costs. Even a 1% gain in accuracy can reshape customer experience and bottom-line efficiency.  

In short: **DeepFusion turns product classification from a tedious bottleneck into a scalable, reliable system.**  

---

## Project Objectives  
The goal of this project is to **automate and improve product categorization at scale**. Specifically, we set out to:  

1. **Build strong baselines**  
   - Train separate models on metadata-only and image-only inputs to understand the standalone strengths of each.  

2. **Design a fusion model**  
   - Combine visual and structured product data into a single deep learning system, capturing richer context than either source alone.  

3. **Evaluate effectiveness with business-relevant metrics**  
   - Use accuracy and F1-score to measure not just overall performance, but also robustness across frequent and rare categories.  

4. **Bridge technical results with real-world impact**  
   - Translate even small accuracy gains into practical benefits: reduced manual rework, faster product onboarding, more consistent search and recommendation results, and stronger fraud detection.  

---


## Data Collection  
To train and evaluate **DeepFusion: Multi-Modal Product Intelligence**, we need a dataset that combines both visual data (product images) and structured metadata. For this project, we used the **E-ComRec Dataset** from Kaggle.  

This dataset is well-suited because it provides:  
- **Product images** – the visual representation customers actually see.  
- **Metadata** – structured attributes like category, gender, color, season, and year.  

Together, these two modalities reflect real-world e-commerce catalogs, where classification and recommendations depend on both how a product looks and what it’s described as.  

---

## Exploratory Data Analysis (EDA)  
Before training, we carried out an exploratory data analysis (EDA) to understand the dataset’s structure, detect quality issues, and uncover insights that shape preprocessing.  

### Key Steps in EDA  
1. **Dataset overview**  
   - Checked dataset size, column types, missing values, and duplicates.  
   - Ensured all product entries had both metadata and image links.  

2. **Data quality checks**  
   - Reviewed numeric ranges, unique values, and string formatting issues (e.g., trailing spaces, inconsistent casing).  

3. **Target distribution**  
   - Analyzed how `articleType` (the target category) was distributed.  
   - Found some classes heavily represented while others were underrepresented — a signal to handle class imbalance.  

4. **Metadata distributions**  
   - Explored categorical features like `gender`, `masterCategory`, `subCategory`, `baseColour`, `season`, and `year`.  
   - Identified patterns such as dominant product colors or seasonal spikes.  

5. **Relationships**  
   - Compared `articleType` against features like `gender` to uncover correlations (e.g., some categories were gender-specific).  

6. **Image validation**  
   - Verified image URLs were valid and accessible.  
   - Checked for missing or broken links.  

7. **Sample inspection**  
   - Displayed a small set of product images alongside their metadata to confirm alignment.  

---

### Sample Visualizations  

![Top 15 Subcategory](https://github.com/user-attachments/assets/6e406a28-19d0-4134-8d31-be622bfc466b)  
*Distribution of Subcategores* 

![Sample Images](https://github.com/user-attachments/assets/1c93af5a-3ba4-40e9-9298-529b9eaec67a)  
*Example products with their metadata attributes.*  

![Article Distrinbution](https://github.com/user-attachments/assets/46629992-1d88-43d5-a38d-df584de0e464)
*Article Distribution by Gender*

![Seasons Distribution](https://github.com/user-attachments/assets/1aa808f1-aa3e-477e-94ca-00cbb14f869d)
*Season Distribution*


## Feature Engineering & Preprocessing  
To prepare the dataset for modeling, we built a preprocessing pipeline that cleaned and transformed the raw inputs into machine-readable features.  

**Steps included:**  
- **Handling missing values**: Filled gaps in numeric fields (like `year`) with median values and replaced missing categorical entries (e.g., `baseColour`, `season`, `usage`) with `"Unknown"`.  
- **Text cleaning**: Standardized the `productDisplayName` by removing special characters, trimming spaces, and converting to lowercase.  
- **Derived features**: Created a new feature, `name_length`, capturing the number of words in each product name.  
- **Encoding categorical variables**: Applied one-hot encoding to fields like gender, category, subcategory, and season.  
- **Scaling numeric variables**: Standardized numeric features (`year`, `name_length`) to ensure balanced contributions during training.  
- **Target encoding**: Converted the target variable (`articleType`) into numeric labels for classification.  

This pipeline was fitted on the training set and then consistently applied to validation and test sets, ensuring no data leakage and a fair comparison across models.  


## Modeling  
Before fusing images with metadata, we first tested each input type on its own. Training separate models on **metadata-only** features and **image-only** data gave us baseline results and helped reveal the unique strengths of each modality.  

To study model behavior, we tried three different setups for both metadata and image pipelines:  
1. **Small baseline model** – A simple network to confirm the pipeline runs correctly end-to-end.  
2. **Overfit model** – A deliberately large network with minimal regularization, used as a diagnostic check to ensure the model can at least memorize the training data.  
3. **Regularized model** – A well-balanced design that includes techniques like dropout, batch normalization, and L2 regularization to improve generalization.  

### Metadata Model  
The regularized metadata model used features like gender, category, color, season, and year. Regularization helped it avoid overfitting and learn useful signals from structured data. Beyond accuracy, we evaluated it with **precision, recall, and F1-score** to confirm performance across both frequent and rare product categories.  

### Image Model  
For images, we built a regularized model on top of a **pre-trained EfficientNetB0 backbone**. By freezing the base model and adding data augmentation plus dropout, the system learned strong visual features without overfitting. This setup provided a powerful image-only baseline.  

### Fusion Model  
Finally, we combined metadata and image inputs into a single **fusion model**. This multi-modal design leveraged both structured attributes and product photos, capturing richer context than either input type alone. The result was our best-performing setup, offering higher accuracy and better consistency across categories.  

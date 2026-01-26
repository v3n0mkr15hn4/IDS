# Intrusion Detection System (IDS) with AI/ML

An advanced network intrusion detection system leveraging deep learning and ensemble methods with explainable AI capabilities.

## 📋 Overview

This project implements a comprehensive intrusion detection system that combines multiple machine learning techniques to detect and classify network attacks. The system uses autoencoders for anomaly detection, Wasserstein GANs for data augmentation, and Random Forest classifiers for both binary and multi-class attack classification.

## ✨ Key Features

- **Binary Classification**: Distinguishes between normal traffic and attacks
- **Multi-Class Classification**: Identifies specific attack types (DDoS, DoS, Bot, Brute Force, Web Attacks, Port Scan, etc.)
- **Data Augmentation**: Uses WGAN to generate synthetic attack samples for balanced training
- **Explainable AI**: Integrates SHAP and LIME for model interpretability
- **Automated Feature Selection**: Uses SelectKBest to identify the most relevant features
- **Comprehensive Evaluation**: Includes confusion matrices, ROC curves, and detailed performance metrics

## 🎯 Supported Attack Types

The system can detect and classify the following attack types:

- **BENIGN**: Normal network traffic
- **DDoS**: Distributed Denial of Service attacks
- **DoS**: Denial of Service (GoldenEye, Hulk, Slowhttptest, slowloris)
- **Bot**: Botnet activities
- **Brute Force**: FTP-Patator, SSH-Patator
- **Web Attacks**: SQL Injection, XSS, Brute Force
- **Port Scan**: Network reconnaissance
- **Infiltration**: Network infiltration attempts
- **Heartbleed**: Heartbleed vulnerability exploitation

## 🔧 Requirements

### Python Packages

```bash
pip install numpy pandas matplotlib seaborn scikit-learn tensorflow shap lime
```

### Dependencies

- **numpy**: Numerical computing
- **pandas**: Data manipulation
- **matplotlib & seaborn**: Visualization
- **scikit-learn**: Machine learning algorithms
- **tensorflow & keras**: Deep learning framework
- **shap**: SHapley Additive exPlanations
- **lime**: Local Interpretable Model-agnostic Explanations

## 📊 Dataset

The system is designed to work with the **CICIDS2017** dataset, which includes:

- Monday-WorkingHours.pcap.csv
- Tuesday-WorkingHours.pcap.csv
- Wednesday-WorkingHours.pcap.csv
- Thursday-WorkingHours-Morning-WebAttacks.pcap.csv
- Thursday-WorkingHours-Afternoon-Infilteration.pcap.csv
- Friday-WorkingHours-Morning.pcap.csv
- Friday-WorkingHours-Afternoon-PortScan.pcap.csv
- Friday-WorkingHours-Afternoon-DDos.pcap.csv

**Note**: If dataset files are not available, the system automatically generates sample data for testing purposes.

## 🏗️ Architecture

### 1. **Autoencoder (AE)**
- **Purpose**: Learn normal network behavior patterns
- **Architecture**: 
  - Encoder: Input → 24 → 20 → 16
  - Decoder: 16 → 20 → 24 → Output
- **Training**: Only on benign (normal) traffic
- **Loss Function**: Mean Squared Error (MSE)

### 2. **Wasserstein GAN (WGAN)**
- **Purpose**: Generate synthetic attack samples for data balancing
- **Generator**: 100 (latent) → 32 → 64 → 16 (encoding)
- **Critic**: 16 → 24 → 16 → 1
- **Gradient Penalty**: λ = 10.0
- **Optimization**: Adam optimizer with β₁=0.0, β₂=0.9

### 3. **Random Forest Classifiers**
- **Binary Classifier**: 
  - Normal vs Attack detection
  - 100 estimators, max_depth=10
- **Multi-Class Classifier**: 
  - Specific attack type identification
  - 100 estimators, max_depth=8
  - Class-balanced weights

## 📈 Performance Metrics

The system evaluates models using:

- **Accuracy**: Overall classification correctness
- **Precision**: True positive rate among predicted positives
- **Recall**: True positive rate among actual positives
- **F1-Score**: Harmonic mean of precision and recall
- **ROC-AUC**: Area under the receiver operating characteristic curve
- **Confusion Matrix**: Detailed classification breakdown

## 🔍 Explainable AI (XAI)

### SHAP (SHapley Additive exPlanations)

- **Summary Plots**: Global feature importance visualization
- **Bar Plots**: Ranked feature importance
- **Dependence Plots**: Feature value vs. SHAP value relationships
- **Waterfall Plots**: Individual prediction explanations
- **Decision Plots**: Multi-class decision boundaries

### LIME (Local Interpretable Model-agnostic Explanations)

- **Local Explanations**: Individual prediction interpretability
- **Feature Contribution**: Identifies which features drove specific predictions
- **Attack-Specific Analysis**: Shows discriminative features for each attack type

## 🚀 Usage

### Running the Complete Pipeline

1. **Load and prepare data**:
   - Place CICIDS2017 CSV files in the working directory
   - Or let the system generate sample data

2. **Execute the notebook**:
   - Run all cells sequentially
   - The system will automatically handle data preprocessing, model training, and evaluation

3. **Analyze results**:
   - Review performance metrics
   - Examine SHAP and LIME explanations
   - Check feature-attack relationships

### Testing New Samples

Use the provided function to analyze new network traffic:

```python
# Analyze a single sample
sample = X_test.iloc[0]
result = corrected_predict_attack_type(sample, show_details=True)

# Results include:
# - Binary classification (Attack/Normal)
# - Confidence scores
# - Predicted attack type
# - Top 3 attack type predictions
```

## 📁 Project Structure

```
Intrusion Detection System.ipynb
├── Section 1-5: Data Loading & Preprocessing
│   ├── Data loading and cleaning
│   ├── Attack type mapping
│   ├── Feature engineering
│   ├── Feature selection (top 50)
│   └── Train/test split (stratified)
├── Section 6-7: Deep Learning Models
│   ├── Autoencoder training
│   └── WGAN training & synthetic data generation
├── Section 8-9: Classification Models
│   ├── Binary classifier (Normal vs Attack)
│   └── Multi-class classifier (Attack types)
├── Section 10-13: Explainable AI
│   ├── SHAP analysis (binary & multi-class)
│   └── LIME analysis (binary & multi-class)
└── Section 14: Validation & Analysis
    ├── Model verification
    ├── Feature-attack relationships
    └── Prediction functions
```

## 🎓 Key Insights

### Data Balancing
- Original dataset often has class imbalance (benign >> attacks)
- WGAN generates synthetic attack samples to achieve ~70% benign, 30% attack ratio
- Prevents model bias toward majority class

### Feature Selection
- Reduces dimensionality from 78+ features to top 50
- Uses ANOVA F-statistic (f_classif)
- Improves model performance and reduces overfitting

### Regularization
- Random Forest max_depth and min_samples parameters prevent overfitting
- Achieves balance between accuracy and generalization

## ⚠️ Important Notes

### Overfitting Detection
The system includes warnings for:
- Accuracy ≥ 99% (suspiciously high)
- Single feature with importance > 0.5 (possible data leakage)

### Data Leakage Prevention
- Target variables (`Label`, `Attack_Type`) explicitly excluded from features
- Attack type mapping preserved before binary conversion
- Proper train/test splitting with stratification

## 🔮 Future Improvements

1. **Real-time Detection**: Implement streaming data processing
2. **Additional Models**: Test with CNN, LSTM, or Transformer architectures
3. **Feature Engineering**: Extract time-series and sequential patterns
4. **Ensemble Methods**: Combine multiple models for improved accuracy
5. **Deployment**: Create API endpoint for production use
6. **Dashboard**: Build real-time monitoring dashboard

## 📚 References

- **CICIDS2017 Dataset**: Canadian Institute for Cybersecurity
- **SHAP**: Lundberg & Lee (2017)
- **LIME**: Ribeiro et al. (2016)
- **WGAN**: Arjovsky et al. (2017)

## 👥 Contributing

This is an academic project for intrusion detection research. Suggestions and improvements are welcome.

## 📄 License

This project is developed for educational and research purposes.

---

**Author**: Vasanth Krishna V

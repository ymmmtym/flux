# Flux2 管理リポジトリ

Flux2を使用したKubernetesのGitOps管理リポジトリです。

## 構成

```
.
├── clusters/          # クラスター設定
│   └── local/        # ローカル環境
├── apps/             # アプリケーション定義
    ├── base/         # ベース設定
    └── overlays/     # 環境別設定
        └── local/    # ローカル環境用
```

## 使い方

### 初回セットアップ

#### Flux CLIを使用

```bash
flux bootstrap github \
  --owner=ymmmtym \
  --repository=flux \
  --path=clusters/local \
  --personal
```

#### Helmを使用

```bash
helm repo add fluxcd-community https://fluxcd-community.github.io/helm-charts

# Flux2コンポーネントをインストール
helm install flux2 fluxcd-community/flux2 \
  --namespace flux-system \
  --create-namespace

# GitRepositoryとKustomizationをインストール
helm install flux2-sync fluxcd-community/flux2-sync \
  --namespace flux-system \
  --set gitRepository.spec.url=https://github.com/ymmmtym/flux \
  --set gitRepository.spec.ref.branch=main \
  --set kustomization.spec.path=./clusters/local
```

### 同期確認

```bash
flux get kustomizations
flux get helmreleases
```

### 手動同期

```bash
flux reconcile kustomization flux-system --with-source
```

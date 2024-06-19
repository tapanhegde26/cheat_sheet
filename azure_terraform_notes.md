##  How to enable diagnostics for AKS cluster using terraform
To enable diagnostics for `Azure Kubernetes Service` (AKS) using Terraform, you can use the `azurerm_monitor_diagnostic_setting` resource in combination with the `azurerm_kubernetes_cluster` resource. Here's a step-by-step description of the Terraform program you can define:

* Create an Azure resource group using the azurerm_resource_group resource.
* Create an Azure storage account using the azurerm_storage_account resource.
* Create an Azure Log Analytics workspace using the azurerm_log_analytics_workspace resource.
* Create an Azure Kubernetes Service (AKS) cluster using the azurerm_kubernetes_cluster resource.
* Enable diagnostics for the AKS cluster using the azurerm_monitor_diagnostic_setting resource.

Terraform code
```
# Create resource group
resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

# Create storage account
resource "azurerm_storage_account" "example" {
  name                     = "storageaccountname"
  resource_group_name      = azurerm_resource_group.example.name
  location                 = azurerm_resource_group.example.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

# Create Log Analytics workspace
resource "azurerm_log_analytics_workspace" "example" {
  name                = "example-log-analytics"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  sku                 = "PerGB2018"
}

# Create AKS cluster
resource "azurerm_kubernetes_cluster" "example" {
  name                = "example-aks"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  dns_prefix          = "example-aks"
  kubernetes_version  = "1.21.2"

  default_node_pool {
    name                = "default"
    node_count          = 1
    vm_size             = "Standard_DS2_v2"
    os_disk_size_gb     = 30
    vnet_subnet_id      = azurerm_subnet.example.id
    enable_auto_scaling = false
  }

  identity {
    type = "SystemAssigned"
  }
}

# Enable diagnostics for AKS cluster
resource "azurerm_monitor_diagnostic_setting" "example" {
  name               = "example-aks-diagnostics"
  target_resource_id = azurerm_kubernetes_cluster.example.id
  storage_account_id = azurerm_storage_account.example.id

  log {
    category = "kube-apiserver"
    enabled  = true
  }

  log {
    category = "kube-controller-manager"
    enabled  = true
  }

  log {
    category = "kube-scheduler"
    enabled  = true
  }

  log {
    category = "kube-audit"
    enabled  = true
  }

  log {
    category = "kube-node"
    enabled  = true
  }

  metric {
    category = "AllMetrics"
    enabled  = true
  }
}
```

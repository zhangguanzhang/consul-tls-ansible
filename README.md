# consul-tls-ansible

> * 全部tls+systemd
> * 理论上只要有systemd都能部署


## 部署
部署文档: https://zhangguanzhang.github.io/2019/09/27/consul-tls/

机器信息，172.19.0.3上安装ansible

| IP          | Hostname |  CPU  |  Memory |   role   | nodeName |
| :-----      |  :----:  |:----: |  :----: |  :----:  |  :----:  |
| 172.19.0.3  |consul1   |  4    |   8G    |  server  | 172.19.0.3  |
| 172.19.0.4  |consul2   |  4    |   8G    |  server  | 172.19.0.4 |
| 172.19.0.5  |consul3   |  4    |   8G    |  server  | 172.19.0.5 |
| 172.19.0.8  |consul4   |  2    |   4G    |  client  | 172.19.0.8 |
| 172.19.0.9  |consul5   |  2    |   4G    |  client  | 172.19.0.9 |


### 准备工作

**1 安装ansible**
```
yum install -y wget epel-release && yum install -y python-pip unzip git sshpass
pip install --no-cache-dir ansible \
  -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
```

**2 准备部署文件**
```
git clone https://github.com/zhangguanzhang/consul-tls-ansible.git
cd consul-tls-ansible
export VERSION=1.6.1 
wget https://releases.hashicorp.com/consul/${VERSION}/consul_${VERSION}_linux_amd64.zip
unzip -x consul_${VERSION}_linux_amd64.zip
```
**3 配置属性**

集群信息编写`inventory/hosts`文件，bootstrap建议最少3个，server要大于等于bootstrap的数量
`group_vars/all.yml`里的`ansible_ssh_pass`为ssh密码


### 运行剧本

**1 基础设置(自己设置好也可以不执行这步)**

```
ansible-playbook 01-setup.yml
```

**2 tls**

```
ansible-playbook 02-tls.yml
```

**3 consul server**

```
ansible-playbook 03-server.yml
```

**4 consul client**

```
ansible-playbook 04-client.yml
```

**5 backup certs**
```
tar -zcvf cert.tar.gz *.pem
```

todo:
  - client这块可能后续会变动以及consul的配置部分，目前只是粗略看了下，没有细看
  - 目前用来做内网dns转发，不需要依赖数据备份，有需要的话可以联系我，我添加备份剧本

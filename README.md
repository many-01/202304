# 202304
<p>创建/删除虚拟机的API代码取决于使用的虚拟化平台。以下是两个常见平台的示例代码：</p>
<ol>
<li>使用VMware vSphere的API</li>
</ol>
<p>创建虚拟机：</p>
<pre><code class="language-python">from pyVim.connect import SmartConnectNoSSL
from pyVmomi import vim

# Connect to the vCenter Server
si = SmartConnectNoSSL(
    host=&#39;[vCenter_Server_IP]&#39;,
    user=&#39;[username]&#39;,
    pwd=&#39;[password]&#39;,
    port=[port]
)

# Get the virtual machine folder
folder = si.content.rootFolder.children[0]
# Get the datastore where the VM will reside
datastore = folder.datastore[0]
# Get the resource pool where the VM will run
resource_pool = folder.resourcePool

# Create the virtual machine configuration
vm_name = &#39;[VM_name]&#39;
vmx_file_path = &#39;[vmx_file_path]&#39;
vmx_file = vim.vm.ConfigSpec(
    files=vim.vm.FileInfo(
        vmPathName=vmx_file_path
    )
)
vm_config = vim.vm.ConfigSpec(
    name=vm_name,
    files=vmx_file,
    memoryMB=[memory_size_in_MB],
    numCPUs=[num_of_vcpu],
    guestId=&#39;[Guest_OS_family]&#39;
)

# Create the virtual machine
task = folder.CreateVM_Task(
    config=vm_config,
    pool=resource_pool,
    datastore=datastore
)
result = task.wait()
print(&#39;Virtual Machine created successfully!&#39;)
</code></pre>
<p>删除虚拟机：</p>
<pre><code class="language-python"># Get the virtual machine object
vm_name = &#39;[VM_name]&#39;
vm = None
for child in folder.childEntity:
    if child.name == vm_name:
        vm = child
        break
if vm is None:
    raise Exception(&#39;Virtual Machine not found!&#39;)

# Power off the virtual machine
task = vm.PowerOffVM_Task()
result = task.wait()
print(&#39;Virtual Machine powered off successfully!&#39;)

# Delete the virtual machine
task = vm.Destroy_Task()
result = task.wait()
print(&#39;Virtual Machine deleted successfully!&#39;)
</code></pre>
<p>需要注意的是，以上代码是基于vSphere API编写的Python脚本，在运行这些代码之前，需要安装PyVmomi模块。</p>


## File cheat codes

### 签名

```solidity
// Reads the entire content of file to string, (path) => (data)
function readFile(string calldata) external returns (string memory);
/// Reads the entire content of file as binary. `path` is relative to the project root.
function readFileBinary(string calldata path) external view returns (bytes memory data);
/// Reads the directory at the given path recursively, up to `maxDepth`.
/// `maxDepth` defaults to 1, meaning only the direct children of the given directory will be returned.
/// Follows symbolic links if `followLinks` is true.
function readDir(string calldata path) external view returns (DirEntry[] memory entries);
// Reads next line of file to string, (path) => (line)
function readLine(string calldata) external returns (string memory);
/// Reads a symbolic link, returning the path that the link points to.
/// This cheatcode will revert in the following situations, but is not limited to just these cases:
/// - `path` is not a symbolic link.
/// - `path` does not exist.
function readLink(string calldata linkPath) external view returns (string memory targetPath);
// Writes data to file, creating a file if it does not exist, and entirely replacing its contents if it does.
// (path, data) => ()
function writeFile(string calldata, string calldata) external;
// Writes line to file, creating a file if it does not exist.
// (path, data) => ()
function writeLine(string calldata, string calldata) external;
// Closes file for reading, resetting the offset and allowing to read it from beginning with readLine.
// (path) => ()
function closeFile(string calldata) external;
// Removes file. This cheatcode will revert in the following situations, but is not limited to just these cases:
// - Path points to a directory.
// - The file doesn't exist.
// - The user lacks permissions to remove the file.
// (path) => ()
function removeFile(string calldata) external;
// Returns true if the given path points to an existing entity, else returns false
// (path) => (bool)
function exists(string calldata) external returns (bool);
// Returns true if the path exists on disk and is pointing at a regular file, else returns false
// (path) => (bool)
function isFile(string calldata) external returns (bool);
// Returns true if the path exists on disk and is pointing at a directory, else returns false
// (path) => (bool)
function isDir(string calldata) external returns (bool);
```

### 描述 

这些作弊码由 [forge-std](https://github.com/foundry-rs/forge-std) 提供，可用于文件系统操作。

默认情况下，文件系统访问被禁止，并且需要在`foundry.toml`中设置`fs_permission`：

```toml
# Configures permissions for cheatcodes that touch the filesystem like `vm.writeFile`
# `access` restricts how the `path` can be accessed via cheatcodes
#    `read-write` | `true`   => `read` + `write` access allowed (`vm.readFile` + `vm.writeFile`)
#    `none`| `false` => no access
#    `read` => only read access (`vm.readFile`)
#    `write` => only write access (`vm.writeFile`)
# The `allowed_paths` further lists the paths that are considered, e.g. `./` represents the project root directory
# By default _no_ fs access permission is granted, and _no_ paths are allowed
# following example enables read access for the project dir _only_:
#       `fs_permissions = [{ access = "read", path = "./"}]`
fs_permissions = [] # default: all file cheat codes are disabled
```

### 例子 

向文件追加一行，如果文件尚不存在，则会创建该文件

这需要对文件/项目根目录具有读取权限

```toml
fs_permissions = [{ access = "read", path = "./"}]
```

```solidity
string memory path = "output.txt";

string memory line1 = "first line";
vm.writeLine(path, line1);

string memory line2 = "second line";
vm.writeLine(path, line2);
```

写入并从文件中读取

这需要对文件/项目根目录具有读写权限：

```toml
fs_permissions = [{ access = "read-write", path = "./"}]
```

```solidity
string memory path = "file.txt";
string memory data = "hello world";
vm.writeFile(path, data);

assertEq(vm.readFile(path), data);
```

删除文件

这需要对文件/项目根目录具有写入权限：

```toml
fs_permissions = [{ access = "write", path = "./"}]
```

```solidity
string memory path = "file.txt";
vm.removeFile(path);

assertFalse(vm.exists(validPath));
```

Verify that a filesystem path is valid

```solidity
// Verify that path 'foo/files/bar.txt' exists
string memory validPath = "foo/files/bar.txt";
assertTrue(vm.exists(validPath));
```

Verify that a filesystem path points to a file or directory

```solidity
// Verify that path 'foo/file/bar.txt' points to a file
string memory validFilePath = "foo/files/bar.txt";
assertTrue(vm.isFile(validFilePath));

// Verify that 'foo/file' points to a directory
string memory validDirPath = "foo/files";
assertTrue(vm.isDir(validDirPath));
```
import os
import sys

if sys.platform.startswith("linux"):
    host_platform = "linux"
elif sys.platform == "darwin":
    host_platform = "osx"
elif sys.platform == "win32" or sys.platform == "msys":
    host_platform = "windows"
else:
    raise ValueError(
        "Could not detect platform automatically, please specify with "
        "platform=<platform>"
    )

env = Environment(ENV=os.environ)

is64 = sys.maxsize > 2**32
if (
    env["TARGET_ARCH"] == "amd64" or
    env["TARGET_ARCH"] == "emt64" or
    env["TARGET_ARCH"] == "x86_64" or
    env["TARGET_ARCH"] == "arm64-v8a"
):
    is64 = True

opts = Variables([], ARGUMENTS)
opts.Add(EnumVariable(
    "platform",
    "Target platform",
    host_platform,
    allowed_values=("linux", "osx", "windows"),
    ignorecase=2
))
opts.Add(EnumVariable(
    "target",
    "Compilation target",
    "debug",
    allowed_values=("debug", "release"),
    ignorecase=2
))
opts.Add(EnumVariable(
    "bits",
    "Target platform bits",
    "64" if is64 else "32",
    ("32", "64")
))
opts.Update(env)

# Generates help for the -h scons option.
Help(opts.GenerateHelpText(env))

if host_platform == "windows":
    if env["bits"] == "64":
        env.Append(TARGET_ARCH="amd64")
    elif env["bits"] == "32":
        env.Append(TARGET_ARCH="x86")

if env["platform"] == "linux":
    if env["target"] == "debug":
        env.Append(CCFLAGS=["-g3", "-Og"])
        env.Append(CXXFLAGS=["-std=c++17"])
    else:
        env.Append(CCFLAGS=["-O3"])
        env.Append(CXXFLAGS=["-std=c++17"])
        env.Append(LINKFLAGS=["-s"])

elif env["platform"] == "osx":
    if env["target"] == "debug":
        env.Append(CCFLAGS=["-g", "-O2", "-arch", "x86_64"])
        env.Append(LINKFLAGS=["-arch", "x86_64"])
    else:
        env.Append(CCFLAGS=["-g", "-O3", "-arch", "x86_64"])
        env.Append(LINKFLAGS=["-arch", "x86_64"])

elif env["platform"] == "windows":
    env.Append(CPPDEFINES=["WIN32", "_WIN32", "_WINDOWS", "_CRT_SECURE_NO_WARNINGS"])
    env.Append(CCFLAGS=["-W3", "-GR"])
    if env["target"] == "debug":
        env.Append(CPPDEFINES=["_DEBUG"])
        env.Append(CCFLAGS=["-MDd", "-ZI"])
        env.Append(LINKFLAGS=["-DEBUG"])
    else:
        env.Append(CPPDEFINES=["NDEBUG"])
        env.Append(CCFLAGS=["-O2", "-MD"])

# Source Files
env.Append(CPPPATH=["src/"])
sources = Glob("src/*.c")

program = env.Program(target="bin/main", source=sources)
Default(program)

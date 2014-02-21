#!/bin/bash
# package - create tarball releases for software packages

set -e

BUILDER="opus3"
FORCE=0
OPTIND=1
PKGDIR="released-packages"

# Prints usage to standard out
usage() {
    echo "Usage: package [-d <pkg-dir>] [-b <build-mach>] <src-dir> <version>"
}

# Parse command-line options
while getopts "hfd:b:" opt; do
    case "$opt" in 
        b)
            BUILDER="$OPTARG"
            ;;
        d)
            PKGDIR="$OPTARG"
            ;;
        f)
            FORCE=1
            ;;
        h)
            usage
            exit 0
            ;;
        *)
            ;;
    esac
done
shift $((OPTIND-1))

SRCDIR=$1
VERSION=$2
PACKAGE="${SRCDIR}-${VERSION}"

# Sanity checks
if [ $# -ne 2 ]; then
    echo "Error: Invalid number of arguments."
    exit 2
elif [ ! -d $PKGDIR ]; then
    echo "Error: $PKGDIR: Not a directory"
    exit 1
elif [ ! -d $SRCDIR ]; then
    echo "Error: $SRCDIR: Not a directory"
    exit 1
fi

# Check for an existing release
if [ -d "${PKGDIR}/${PACKAGE}" ]; then
    if [ $FORCE -eq 0 ]; then
        echo "Error: A release for ${PACKAGE} already exists."
        exit 1
    else
        rm -rf "${PKGDIR}/${PACKAGE}"
    fi
fi

# Tar archive already exists
if [ -f "${PKGDIR}/${PACKAGE}.tar.gz" ]; then
    if [ $FORCE -eq 0 ]; then
        echo "Error: Archive ${PACKAGE}.tar.gz already exists."
        exit 1
    else
        rm -f "${PKGDIR}/${PACKAGE}.tar.gz"
    fi
fi

echo "Copying source to ${PKGDIR}/${PACKAGE}..."
cp -r "$SRCDIR" "${PKGDIR}/${PACKAGE}"

echo "Deleting unnecessary files..."
rm -rf "${PKGDIR}/${PACKAGE}/.git" "${PKGDIR}/${PACKAGE}/.gitignore"

echo "Creating archive ${PKGDIR}/${PACKAGE}.tar.gz..."
tar -C "${PKGDIR}" -czvf "${PKGDIR}/${PACKAGE}.tar.gz" "${PACKAGE}"

echo "Copying to build machine..."
scp "${PKGDIR}/${PACKAGE}.tar.gz" "${BUILDER}:~/rpmbuild/SOURCES/"

#" vim: filetype=sh

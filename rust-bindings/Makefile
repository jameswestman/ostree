GIR_REPO := https://github.com/gtk-rs/gir.git
GIR_VERSION := e8f82cf63f2b2fba7af9440248510c4b7e5ab787
OSTREE_REPO := ../ostree
OSTREE_VERSION := patch-v2022.2
RUSTDOC_STRIPPER_VERSION := 0.1.17

all: gir

.PHONY: gir gir-report update-gir-files remove-gir-files merge-lgpl-docs ci-build-stages


# -- gir generation --
target/tools/bin/gir:
	cargo install --root target/tools --git $(GIR_REPO) --rev $(GIR_VERSION) -- gir

gir: target/tools/bin/gir
	target/tools/bin/gir -c conf/ostree-sys.toml
	target/tools/bin/gir -c conf/ostree.toml

gir-report: target/tools/bin/gir
	target/tools/bin/gir -c conf/ostree.toml -m not_bound


# -- LGPL docs generation --
target/tools/bin/rustdoc-stripper:
	cargo install --root target/tools --version $(RUSTDOC_STRIPPER_VERSION) -- rustdoc-stripper

merge-lgpl-docs: target/tools/bin/gir target/tools/bin/rustdoc-stripper
	target/tools/bin/gir -c conf/ostree.toml -m doc
	target/tools/bin/rustdoc-stripper -g -o target/vendor.md


# -- gir file management --
update-gir-files: \
	remove-gir-files \
	gir-files \
	gir-files/GLib-2.0.gir \
	gir-files/Gio-2.0.gir \
	gir-files/GObject-2.0.gir \
	gir-files/OSTree-1.0.gir

remove-gir-files:
	rm -f gir-files/*.gir

gir-files:
	mkdir -p gir-files

%.gir:
	curl -o $@ -L https://github.com/gtk-rs/gir-files/raw/master/${@F}

gir-files/OSTree-1.0.gir:
	ln -sfr ../OSTree-1.0.gir $@

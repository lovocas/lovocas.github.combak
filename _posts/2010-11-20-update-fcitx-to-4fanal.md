--- 
date: 2010-11-20 17:12:31 +08:00
layout: post
title: !binary |
    5oqK5Lit5paH6L6T5YWl5rOV5o2i5oiQ5LqGRmNpdHjmnIDmlrDniYg=

wordpress_id: 535
wordpress_url: !binary |
    aHR0cDovLzEyNy4wLjAuMTo4MDgwL3dwLz9wPTUzNQ==

---
前段时间因为在Fcitx3.6.3输入法在Firefox中会出现按Backspace一次会删除两个字符的现象，在没有办法解决的情况下就把中文输入法换成了Ibus，用起来确实不如Fcitx习惯。
这段时间就看到Fcitx网站上有4.0的截图，看起来确实不错，刚好又发现4.0最终版放出，于是就安装上试了下，用起来果然不一样，舒服，而且也很好看，速度很快，安装过程简
录如下：
由于我使用的Linux发行版是Gentoo，安装最方便的莫过于直接使用ebuild文件。
[code lang='plain']
# Copyright 1999-2010 Gentoo Foundation
# Distributed under the terms of the GNU General Public License v2
# $Header: /var/cvsroot/gentoo-x86/app-i18n/fcitx/fcitx-3.6.3.ebuild,v 1.1 2010/02/14 04:35:54 matsuu Exp $

EAPI=2
inherit eutils

DESCRIPTION="Free Chinese Input Toy for X. Another Chinese XIM Input Method"
HOMEPAGE="http://code.google.com/p/fcitx/"
SRC_URI="http://fcitx.googlecode.com/files/${P}.tar.gz"

LICENSE="GPL-2"
SLOT="0"
KEYWORDS="~amd64"
IUSE="dbus +pango"

RDEPEND="x11-libs/cairo
	x11-libs/libX11
	x11-libs/libXft
	x11-libs/libXrender
	pango? ( x11-libs/pango )
	dbus? ( >=sys-apps/dbus-0.2 )"
DEPEND="${RDEPEND}
	x11-proto/xproto
	dev-util/pkgconfig"

src_configure() {
	# --disable-xft doesn't work
	# econf $(use_enable xft) || die
	econf $(use_enable dbus)
	$(use_enable pango)|| die
}

src_install() {
	emake DESTDIR="${D}" install || die

	dodoc AUTHORS ChangeLog README THANKS TODO || die

	rm -rf "${D}"/usr/share/fcitx/doc/ || die
	dodoc doc/pinyin.txt doc/cjkvinput.txt doc/fcitx4.pdf || die
	dohtml doc/wb_fh.htm || die
}

pkg_postinst() {
	elog
	elog "You should export the following variables to use fcitx"
	elog " export XMODIFIERS="@im=fcitx""
	elog " export GTK_IM_MODULE=xim"
	elog " export QT_IM_MODULE=xim"
	elog
}
[/code]
在安装时可能遇到软件包被mask的问题，只要在/etc/portage/package.unmask和/etc/portage/package.keywords加入相应的条目就可以了
然后直接 emerge -av fcitx
按照提示在.xprofile里加入相应内容，就完成。

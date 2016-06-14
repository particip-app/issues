# Overview: pARTicip Data Provisioning between Server and Apps

# 1. Introduction

## 1.1 Overview of the Data Exchange
The pARTicip App („App“) has been developed during the pARTicipate project at the BFH, from 2014–2016. The BFH-TI institutes RISIS and ICTM actively participated in the overall activities of the project execution (defining features, choosing designs, carrying out tests, etc.), and are in particular responsible for building the App and the Backend Server („Server”) software.

## 1.2 Purpose of this Document

The purpose of this document is
*	to document the data structures employed on data storage and transmission ways
* to define the mapping between user interation on the Typo3 Backend and the data which are served to Apps


# 2. Data Structures

The data structures on the server are show in Appendix A: MySQL Database Schema.

## 2.1 Core Entities

All entities are documented with their attributes on http://particip-app.ch/service/apiv1

- The core entity is `artwork` (MySQL Table `tx_participdata_domain_model_artwork`, enriched data fields by Web service, some filtering of typos, too) describing artwoks
- Other independent, related entities are
  - [1:1] `collection` (MySQL table `tx_participdata_domain_model_collection`) decribing the collection (Kunst Freiburg, kiÖR Bern) and
  - [1:1] `kind` (MySQL table `tx_participdata_domain_model_kind`) decribing the type of artwork
  - [0:n] `artist` (MySQL table `tx_participdata_domain_model_artist`) as a list of the artists involved with an artwork
  - [0:n] `material` (MySQL table `tx_participdata_domain_model_material`) as a list of materials related to the artwork
  - [0:n] `image`s; are to be loaded directly from server URL
- Relationships between `artwork` to `artist`, `material`, and `image` entities are
- [0:n] `artwork_artist_mm` (MySQL table `tx_participdata_artwork_artist_mm`) describing the relation to the
- [0:n] `artwork_material_mm` (MySQL table `tx_participdata_artwork_material_mm`)
- [0:n] `image` (no MySQL table counterpart, computed by Web Service)

## 2.2 `artwork` Entity Lifecycle: Editor View

Artwork entities are the central part of the collections managed on the Typo3 Backend. The following states can be enforced:

- `ACTIVE`  the entity is active, can be seen via the Web service, is visible on the Mobile Website
- `DELETED` the entity is (soft) deleted, i.e., its `deleted` attribute carries the value `1`; once an `artwork` is deleted by an editor on Typo3 by clicking on the `trash icon`, it disappears from her visibility (but is still in the MySQL DB)
- `HIDDEN` the entity is hidden to the outside but visible to the Typo3 editor, its `hidden` attribute carries the value `1`; the object then displays the `white bulb icon`, as opposed to the `yellow bulb icon` (which is displayed in normal state)

## 2.2 `artwork` Entity Lifecycle: Web Service Behavior

The Web Service with its programmed/programmable logic will provide the following behavior:

- `ACTIVE`
 - the entity `artwork` will be served containing the object
 - `artwork_artist_mm`, `artwork_material_mm`, and `image` will contain references to the object's `uid`
- `DELETED`
 - the entity *will be served* with a `deleted` attribute of value `1`
 - `artwork_artist_mm`, `artwork_material_mm`, and `image` WILL NOT contain references to the object's `uid`
- `HIDDEN`
 - the entity *is not transported* by the Web service
 - `artwork_artist_mm`, `artwork_material_mm`, and `image` WILL NOT contain references to the object's `uid`

# Appendix A: MySQL Database Schema

```
/*
 Navicat MySQL Data Transfer

 Source Server         : pARTicip-app.ch Typo3
 Target Server Type    : MySQL
 File Encoding         : utf-8

 Date: 06/13/2016 15:54:57 PM
*/

SET NAMES utf8;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
--  Table structure for `tx_participdata_artwork_artist_mm`
-- ----------------------------
DROP TABLE IF EXISTS `tx_participdata_artwork_artist_mm`;
CREATE TABLE `tx_participdata_artwork_artist_mm` (
  `uid_local` int(11) unsigned NOT NULL DEFAULT '0',
  `uid_foreign` int(11) unsigned NOT NULL DEFAULT '0',
  `sorting` int(11) unsigned NOT NULL DEFAULT '0',
  `sorting_foreign` int(11) unsigned NOT NULL DEFAULT '0',
  `tstamp_manual` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT 'Manually added rlm1',
  KEY `uid_local` (`uid_local`),
  KEY `uid_foreign` (`uid_foreign`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- ----------------------------
--  Table structure for `tx_participdata_artwork_material_mm`
-- ----------------------------
DROP TABLE IF EXISTS `tx_participdata_artwork_material_mm`;
CREATE TABLE `tx_participdata_artwork_material_mm` (
  `uid_local` int(11) unsigned NOT NULL DEFAULT '0',
  `uid_foreign` int(11) unsigned NOT NULL DEFAULT '0',
  `sorting` int(11) unsigned NOT NULL DEFAULT '0',
  `sorting_foreign` int(11) unsigned NOT NULL DEFAULT '0',
  `tstamp_manual` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT 'Manually added rlm1',
  KEY `uid_local` (`uid_local`),
  KEY `uid_foreign` (`uid_foreign`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- ----------------------------
--  Table structure for `tx_participdata_domain_model_artist`
-- ----------------------------
DROP TABLE IF EXISTS `tx_participdata_domain_model_artist`;
CREATE TABLE `tx_participdata_domain_model_artist` (
  `uid` int(11) NOT NULL AUTO_INCREMENT,
  `pid` int(11) NOT NULL DEFAULT '0',
  `firstname` varchar(255) NOT NULL DEFAULT '',
  `lastname` varchar(255) NOT NULL DEFAULT '',
  `homepage` varchar(255) NOT NULL DEFAULT '',
  `wikipedia` varchar(255) NOT NULL DEFAULT '',
  `tstamp` int(11) unsigned NOT NULL DEFAULT '0',
  `crdate` int(11) unsigned NOT NULL DEFAULT '0',
  `cruser_id` int(11) unsigned NOT NULL DEFAULT '0',
  `deleted` tinyint(4) unsigned NOT NULL DEFAULT '0',
  `hidden` tinyint(4) unsigned NOT NULL DEFAULT '0',
  `starttime` int(11) unsigned NOT NULL DEFAULT '0',
  `endtime` int(11) unsigned NOT NULL DEFAULT '0',
  `t3ver_oid` int(11) NOT NULL DEFAULT '0',
  `t3ver_id` int(11) NOT NULL DEFAULT '0',
  `t3ver_wsid` int(11) NOT NULL DEFAULT '0',
  `t3ver_label` varchar(255) NOT NULL DEFAULT '',
  `t3ver_state` tinyint(4) NOT NULL DEFAULT '0',
  `t3ver_stage` int(11) NOT NULL DEFAULT '0',
  `t3ver_count` int(11) NOT NULL DEFAULT '0',
  `t3ver_tstamp` int(11) NOT NULL DEFAULT '0',
  `t3ver_move_id` int(11) NOT NULL DEFAULT '0',
  `sys_language_uid` int(11) NOT NULL DEFAULT '0',
  `l10n_parent` int(11) NOT NULL DEFAULT '0',
  `l10n_diffsource` mediumblob,
  `yearofbirth` int(11) NOT NULL DEFAULT '0',
  `yearofdeath` int(11) NOT NULL DEFAULT '0',
  `fullname` varchar(255) NOT NULL DEFAULT '',
  `artistname` varchar(255) NOT NULL DEFAULT '',
  `sikart` varchar(255) NOT NULL DEFAULT '',
  PRIMARY KEY (`uid`),
  KEY `parent` (`pid`),
  KEY `t3ver_oid` (`t3ver_oid`,`t3ver_wsid`),
  KEY `language` (`l10n_parent`,`sys_language_uid`)
) ENGINE=InnoDB AUTO_INCREMENT=118 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Table structure for `tx_participdata_domain_model_artwork`
-- ----------------------------
DROP TABLE IF EXISTS `tx_participdata_domain_model_artwork`;
CREATE TABLE `tx_participdata_domain_model_artwork` (
  `uid` int(11) NOT NULL AUTO_INCREMENT,
  `pid` int(11) NOT NULL DEFAULT '0',
  `community` varchar(255) NOT NULL DEFAULT '',
  `material` text NOT NULL,
  `originalkey` varchar(255) NOT NULL DEFAULT '',
  `canton` varchar(255) NOT NULL DEFAULT '',
  `artist` varchar(255) NOT NULL DEFAULT '',
  `title` varchar(255) NOT NULL DEFAULT '',
  `lon` varchar(255) NOT NULL DEFAULT '',
  `lat` varchar(255) NOT NULL DEFAULT '',
  `yearofmanufacturing` varchar(255) NOT NULL DEFAULT '',
  `wikipedia` varchar(255) NOT NULL DEFAULT '',
  `description` text NOT NULL,
  `intention` text NOT NULL,
  `sources` text NOT NULL,
  `homepage` varchar(255) NOT NULL DEFAULT '',
  `coordlastupdate` int(11) NOT NULL DEFAULT '0',
  `positiontext` text NOT NULL,
  `tstamp` int(11) unsigned NOT NULL DEFAULT '0',
  `crdate` int(11) unsigned NOT NULL DEFAULT '0',
  `cruser_id` int(11) unsigned NOT NULL DEFAULT '0',
  `deleted` tinyint(4) unsigned NOT NULL DEFAULT '0',
  `hidden` tinyint(4) unsigned NOT NULL DEFAULT '0',
  `starttime` int(11) unsigned NOT NULL DEFAULT '0',
  `endtime` int(11) unsigned NOT NULL DEFAULT '0',
  `t3ver_oid` int(11) NOT NULL DEFAULT '0',
  `t3ver_id` int(11) NOT NULL DEFAULT '0',
  `t3ver_wsid` int(11) NOT NULL DEFAULT '0',
  `t3ver_label` varchar(255) NOT NULL DEFAULT '',
  `t3ver_state` tinyint(4) NOT NULL DEFAULT '0',
  `t3ver_stage` int(11) NOT NULL DEFAULT '0',
  `t3ver_count` int(11) NOT NULL DEFAULT '0',
  `t3ver_tstamp` int(11) NOT NULL DEFAULT '0',
  `t3ver_move_id` int(11) NOT NULL DEFAULT '0',
  `sys_language_uid` int(11) NOT NULL DEFAULT '0',
  `l10n_parent` int(11) NOT NULL DEFAULT '0',
  `l10n_diffsource` mediumblob,
  `material_ref` int(11) unsigned NOT NULL DEFAULT '0',
  `artist_ref` int(11) unsigned NOT NULL DEFAULT '0',
  `collection_ref` int(11) unsigned DEFAULT '0',
  `thumbnail` int(11) unsigned NOT NULL DEFAULT '0',
  `images` int(11) unsigned NOT NULL DEFAULT '0',
  `materialshorttext` varchar(255) NOT NULL DEFAULT '',
  `materialannotated` varchar(255) NOT NULL DEFAULT '',
  `studies` text NOT NULL,
  `listtitle` varchar(255) NOT NULL DEFAULT '',
  `kind_ref` int(11) unsigned DEFAULT '0',
  `tx_extbase_type` varchar(255) NOT NULL DEFAULT '',
  `squares` int(11) unsigned NOT NULL DEFAULT '0',
  `street` varchar(255) NOT NULL DEFAULT '',
  `streetnumber` varchar(255) NOT NULL DEFAULT '',
  `location` varchar(255) NOT NULL DEFAULT '',
  `zip` varchar(255) NOT NULL DEFAULT '',
  `positioncontext` varchar(255) NOT NULL DEFAULT '',
  `positionaccessibility` varchar(255) NOT NULL DEFAULT '',
  `positionroofing` varchar(255) NOT NULL DEFAULT '',
  `positionground` varchar(255) NOT NULL DEFAULT '',
  `positionvegetation` varchar(255) NOT NULL DEFAULT '',
  `positiontraffic` varchar(255) NOT NULL DEFAULT '',
  `positionother` varchar(255) NOT NULL DEFAULT '',
  PRIMARY KEY (`uid`),
  KEY `parent` (`pid`),
  KEY `t3ver_oid` (`t3ver_oid`,`t3ver_wsid`),
  KEY `language` (`l10n_parent`,`sys_language_uid`)
) ENGINE=InnoDB AUTO_INCREMENT=49900 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Table structure for `tx_participdata_domain_model_collection`
-- ----------------------------
DROP TABLE IF EXISTS `tx_participdata_domain_model_collection`;
CREATE TABLE `tx_participdata_domain_model_collection` (
  `uid` int(11) NOT NULL AUTO_INCREMENT,
  `pid` int(11) NOT NULL DEFAULT '0',
  `contact_address` varchar(255) NOT NULL DEFAULT '',
  `contact_email` varchar(255) NOT NULL DEFAULT '',
  `tstamp` int(11) unsigned NOT NULL DEFAULT '0',
  `crdate` int(11) unsigned NOT NULL DEFAULT '0',
  `cruser_id` int(11) unsigned NOT NULL DEFAULT '0',
  `deleted` tinyint(4) unsigned NOT NULL DEFAULT '0',
  `hidden` tinyint(4) unsigned NOT NULL DEFAULT '0',
  `starttime` int(11) unsigned NOT NULL DEFAULT '0',
  `endtime` int(11) unsigned NOT NULL DEFAULT '0',
  `t3ver_oid` int(11) NOT NULL DEFAULT '0',
  `t3ver_id` int(11) NOT NULL DEFAULT '0',
  `t3ver_wsid` int(11) NOT NULL DEFAULT '0',
  `t3ver_label` varchar(255) NOT NULL DEFAULT '',
  `t3ver_state` tinyint(4) NOT NULL DEFAULT '0',
  `t3ver_stage` int(11) NOT NULL DEFAULT '0',
  `t3ver_count` int(11) NOT NULL DEFAULT '0',
  `t3ver_tstamp` int(11) NOT NULL DEFAULT '0',
  `t3ver_move_id` int(11) NOT NULL DEFAULT '0',
  `sys_language_uid` int(11) NOT NULL DEFAULT '0',
  `l10n_parent` int(11) NOT NULL DEFAULT '0',
  `l10n_diffsource` mediumblob,
  `title` varchar(255) NOT NULL DEFAULT '',
  `contactname` varchar(255) NOT NULL DEFAULT '',
  `contactaddress` varchar(255) NOT NULL DEFAULT '',
  `contactemail` varchar(255) NOT NULL DEFAULT '',
  PRIMARY KEY (`uid`),
  KEY `parent` (`pid`),
  KEY `t3ver_oid` (`t3ver_oid`,`t3ver_wsid`),
  KEY `language` (`l10n_parent`,`sys_language_uid`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Table structure for `tx_participdata_domain_model_kind`
-- ----------------------------
DROP TABLE IF EXISTS `tx_participdata_domain_model_kind`;
CREATE TABLE `tx_participdata_domain_model_kind` (
  `uid` int(11) NOT NULL AUTO_INCREMENT,
  `pid` int(11) NOT NULL DEFAULT '0',
  `title` varchar(255) NOT NULL DEFAULT '',
  `tstamp` int(11) unsigned NOT NULL DEFAULT '0',
  `crdate` int(11) unsigned NOT NULL DEFAULT '0',
  `cruser_id` int(11) unsigned NOT NULL DEFAULT '0',
  `deleted` tinyint(4) unsigned NOT NULL DEFAULT '0',
  `hidden` tinyint(4) unsigned NOT NULL DEFAULT '0',
  `starttime` int(11) unsigned NOT NULL DEFAULT '0',
  `endtime` int(11) unsigned NOT NULL DEFAULT '0',
  `t3ver_oid` int(11) NOT NULL DEFAULT '0',
  `t3ver_id` int(11) NOT NULL DEFAULT '0',
  `t3ver_wsid` int(11) NOT NULL DEFAULT '0',
  `t3ver_label` varchar(255) NOT NULL DEFAULT '',
  `t3ver_state` tinyint(4) NOT NULL DEFAULT '0',
  `t3ver_stage` int(11) NOT NULL DEFAULT '0',
  `t3ver_count` int(11) NOT NULL DEFAULT '0',
  `t3ver_tstamp` int(11) NOT NULL DEFAULT '0',
  `t3ver_move_id` int(11) NOT NULL DEFAULT '0',
  `sys_language_uid` int(11) NOT NULL DEFAULT '0',
  `l10n_parent` int(11) NOT NULL DEFAULT '0',
  `l10n_diffsource` mediumblob,
  `titlede` varchar(255) NOT NULL DEFAULT '',
  `titlefr` varchar(255) NOT NULL DEFAULT '',
  `titleen` varchar(255) NOT NULL DEFAULT '',
  `titleit` varchar(255) NOT NULL DEFAULT '',
  `code` varchar(255) NOT NULL DEFAULT '',
  PRIMARY KEY (`uid`),
  KEY `parent` (`pid`),
  KEY `t3ver_oid` (`t3ver_oid`,`t3ver_wsid`),
  KEY `language` (`l10n_parent`,`sys_language_uid`)
) ENGINE=InnoDB AUTO_INCREMENT=14 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Table structure for `tx_participdata_domain_model_material`
-- ----------------------------
DROP TABLE IF EXISTS `tx_participdata_domain_model_material`;
CREATE TABLE `tx_participdata_domain_model_material` (
  `uid` int(11) NOT NULL AUTO_INCREMENT,
  `pid` int(11) NOT NULL DEFAULT '0',
  `title` varchar(255) NOT NULL DEFAULT '',
  `description` text NOT NULL,
  `tstamp` int(11) unsigned NOT NULL DEFAULT '0',
  `crdate` int(11) unsigned NOT NULL DEFAULT '0',
  `cruser_id` int(11) unsigned NOT NULL DEFAULT '0',
  `deleted` tinyint(4) unsigned NOT NULL DEFAULT '0',
  `hidden` tinyint(4) unsigned NOT NULL DEFAULT '0',
  `starttime` int(11) unsigned NOT NULL DEFAULT '0',
  `endtime` int(11) unsigned NOT NULL DEFAULT '0',
  `t3ver_oid` int(11) NOT NULL DEFAULT '0',
  `t3ver_id` int(11) NOT NULL DEFAULT '0',
  `t3ver_wsid` int(11) NOT NULL DEFAULT '0',
  `t3ver_label` varchar(255) NOT NULL DEFAULT '',
  `t3ver_state` tinyint(4) NOT NULL DEFAULT '0',
  `t3ver_stage` int(11) NOT NULL DEFAULT '0',
  `t3ver_count` int(11) NOT NULL DEFAULT '0',
  `t3ver_tstamp` int(11) NOT NULL DEFAULT '0',
  `t3ver_move_id` int(11) NOT NULL DEFAULT '0',
  `sys_language_uid` int(11) NOT NULL DEFAULT '0',
  `l10n_parent` int(11) NOT NULL DEFAULT '0',
  `l10n_diffsource` mediumblob,
  `material_archive_id` int(11) NOT NULL DEFAULT '0',
  `materialarchiveid` int(11) NOT NULL DEFAULT '0',
  `used` tinyint(1) unsigned NOT NULL DEFAULT '0',
  `wikipedia` varchar(255) NOT NULL DEFAULT '',
  PRIMARY KEY (`uid`),
  KEY `parent` (`pid`),
  KEY `t3ver_oid` (`t3ver_oid`,`t3ver_wsid`),
  KEY `language` (`l10n_parent`,`sys_language_uid`)
) ENGINE=InnoDB AUTO_INCREMENT=1060 DEFAULT CHARSET=utf8;

-- ----------------------------
--  Table structure for `tx_participlabels_domain_model_string`
-- ----------------------------
DROP TABLE IF EXISTS `tx_participlabels_domain_model_string`;
CREATE TABLE `tx_participlabels_domain_model_string` (
  `uid` int(11) NOT NULL AUTO_INCREMENT,
  `pid` int(11) NOT NULL DEFAULT '0',
  `value` text NOT NULL,
  `tstamp` int(11) unsigned NOT NULL DEFAULT '0',
  `crdate` int(11) unsigned NOT NULL DEFAULT '0',
  `cruser_id` int(11) unsigned NOT NULL DEFAULT '0',
  `deleted` tinyint(4) unsigned NOT NULL DEFAULT '0',
  `hidden` tinyint(4) unsigned NOT NULL DEFAULT '0',
  `starttime` int(11) unsigned NOT NULL DEFAULT '0',
  `endtime` int(11) unsigned NOT NULL DEFAULT '0',
  `t3ver_oid` int(11) NOT NULL DEFAULT '0',
  `t3ver_id` int(11) NOT NULL DEFAULT '0',
  `t3ver_wsid` int(11) NOT NULL DEFAULT '0',
  `t3ver_label` varchar(255) NOT NULL DEFAULT '',
  `t3ver_state` tinyint(4) NOT NULL DEFAULT '0',
  `t3ver_stage` int(11) NOT NULL DEFAULT '0',
  `t3ver_count` int(11) NOT NULL DEFAULT '0',
  `t3ver_tstamp` int(11) NOT NULL DEFAULT '0',
  `t3ver_move_id` int(11) NOT NULL DEFAULT '0',
  `sys_language_uid` int(11) NOT NULL DEFAULT '0',
  `l10n_parent` int(11) NOT NULL DEFAULT '0',
  `l10n_diffsource` mediumblob,
  `categories` int(11) NOT NULL DEFAULT '0',
  `sorting` int(11) NOT NULL DEFAULT '0',
  `string_id` varchar(255) NOT NULL DEFAULT '',
  `screen` int(11) NOT NULL DEFAULT '0',
  `type` int(11) NOT NULL DEFAULT '0',
  PRIMARY KEY (`uid`),
  KEY `parent` (`pid`),
  KEY `t3ver_oid` (`t3ver_oid`,`t3ver_wsid`),
  KEY `language` (`l10n_parent`,`sys_language_uid`)
) ENGINE=InnoDB AUTO_INCREMENT=93 DEFAULT CHARSET=utf8;

```

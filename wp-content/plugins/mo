<?php
/**
 * @package Mowes
 * @version 1.0.0
 */
/*
Plugin Name: MoWes
Plugin URI: http://dinkominfo.purbalinggakab.go.id/
Description: Monitoring website
Author: Dinkominfo Purbalingga
Version: 1.0.0
Author URI: http://dinkominfo.purbalinggakab.go.id/
*/

// monitor current month
// GET http://localhost:10000/?rest_route=/mowes/v1/monitor
// monitor all
// GET http://localhost:10000/?rest_route=/mowes/v1/monitor-all
class RestControllerMowes extends WP_REST_Controller {
  public function register_routes() {
    register_rest_route('mowes/v1', '/monitor', array(
      array(
        'methods' => WP_REST_Server::READABLE,
        'callback' => array($this, 'monitor'),
        'args' => array(),
      )
    ));
    register_rest_route('mowes/v1', '/monitor-all', array(
      array(
        'methods' => WP_REST_Server::READABLE,
        'callback' => array($this, 'monitor_all'),
        'args' => array(),
      )
    ));
  }

  function monitor() {
    $current_month_only = true;
    return $this->get_data($current_month_only);
  }

  function monitor_all() {
    $current_month_only = false;
    return $this->get_data($current_month_only);
  }

  function get_data($current_month_only) {
    $posts = $this->get_posts($current_month_only);
    $pages = $this->get_pages();

    $res = new stdClass();
    $res->month = $this->get_month();
    $res->year = $this->get_year();
    $res->post = new stdClass();
    $res->post->total = count($posts);
    $res->post->detail = $posts;
    $res->page = new stdClass();
    $res->page->total = count($pages);
    $res->page->detail = $pages;
    return new WP_REST_Response($res);
  }

  function get_month() {
    return (int) (new DateTime())->format('n');
  }

  function get_year() {
    return (int) (new DateTime())->format('Y');
  }

  function get_posts($current_month_only = true) {
    if($current_month_only) {
      $query = 'year=' . $this->get_year() . '&monthnum=' . $this->get_month() . '&nopaging=true';
    } else {
      $query = 'status=published&nopaging=true';
    }

    $query = new WP_Query($query);
    $posts = $query->posts;
    $posts = array_map(function($post) {
      $new_post = new stdClass();
      $new_post->ID = $post->ID;
      $new_post->post_title = $post->post_title;
      $new_post->guid = $post->guid;
      $new_post->post_date_gmt = $post->post_date_gmt;
      return $new_post;
    }, $posts);
    return $posts;
  }

  function get_pages() {
    $query = new WP_Query('post_type=page');
    $posts = $query->posts;
    $posts = array_map(function($post) {
      $new_post = new stdClass();
      $new_post->ID = $post->ID;
      $new_post->post_title = $post->post_title;
      $new_post->guid = $post->guid;
      $new_post->post_date_gmt = $post->post_date_gmt;
      return $new_post;
    }, $posts);
    return $posts;
  }
}

function register_rest_controller_mowes() {
  error_log("Register RestControllerMowes", 0);
  $controller = new RestControllerMowes();
  $controller->register_routes();
}

add_action('rest_api_init', 'register_rest_controller_mowes'); 


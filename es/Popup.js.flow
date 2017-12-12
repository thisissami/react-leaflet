// @flow

import { Popup as LeafletPopup } from 'leaflet'
import PropTypes from 'prop-types'
import { Children, type Element } from 'react'
import { render, unmountComponentAtNode } from 'react-dom'

import MapComponent from './MapComponent'
import latlng from './propTypes/latlng'
import layer from './propTypes/layer'
import map from './propTypes/map'
import type { LatLng } from './types'

type LeafletElement = LeafletPopup
type Props = {
  autoPan?: boolean,
  children: Element<any>,
  isFocused?: boolean, //sami
  onClose?: () => void,
  onOpen?: () => void,
  pane?: string,
  position?: LatLng,
}

export default class Popup extends MapComponent<LeafletElement, Props> {
  static propTypes = {
    children: PropTypes.node,
    isFocused: PropTypes.bool, //sami
    onClose: PropTypes.func,
    onOpen: PropTypes.func,
    position: latlng,
  }

  static contextTypes = {
    map: map,
    popupContainer: layer,
    pane: PropTypes.string,
  }

  getOptions(props: Props): Props {
    return {
      ...super.getOptions(props),
      autoPan: false,
    }
  }

  createLeafletElement(props: Props): LeafletElement {
    return new LeafletPopup(this.getOptions(props), this.context.popupContainer)
  }

  updateLeafletElement(fromProps: Props, toProps: Props) {
    if (toProps.position !== fromProps.position) {
      this.leafletElement.setLatLng(toProps.position)
    }
    //sami
    if (toProps.isFocused !== fromProps.isFocused) {
      if (toProps.isFocused === true) {
        setTimeout(() => {
          window.le = this.leafletElement
          console.log('inside popup timeout', this.leafletElement._contentNode.firstElementChild)
          this.leafletElement._contentNode.firstElementChild.firstElementChild.focus()
        }, 5)
      }
    }
  }

  componentWillMount() {
    super.componentWillMount()
    this.leafletElement = this.createLeafletElement(this.props)
    this.leafletElement.options.autoPan = this.props.autoPan !== false

    this.context.map.on({
      popupopen: this.onPopupOpen,
      popupclose: this.onPopupClose,
    })
  }

  componentDidMount() {
    const { position } = this.props
    const { map, popupContainer } = this.context
    const el = this.leafletElement

    if (popupContainer) {
      // Attach to container component
      popupContainer.bindPopup(el)
    } else {
      // Attach to a Map
      if (position) {
        el.setLatLng(position)
      }
      el.openOn(map)
    }
    //sami
    if (this.props.isFocused) {
      setTimeout(() => {
        console.log('inside didMount', el)
        el._contentNode.firstElementChild.firstElementChild.focus()
      }, 5)
    }
  }

  componentDidUpdate(prevProps: Props) {
    this.updateLeafletElement(prevProps, this.props)

    if (this.leafletElement.isOpen()) {
      this.renderPopupContent()
    }

    //sami
    if (this.props.isFocused) {
      const el = this.leafletElement
      setTimeout(() => {
        console.log('inside didUpdate', el)
        el._contentNode.firstElementChild.firstElementChild.focus()
      }, 5)
    }
  }

  componentWillUnmount() {
    this.removePopupContent()

    this.context.map.off({
      popupopen: this.onPopupOpen,
      popupclose: this.onPopupClose,
    })
    this.context.map.removeLayer(this.leafletElement)

    super.componentWillUnmount()
  }

  onPopupOpen = ({ popup }: { popup: LeafletElement }) => {
    if (popup === this.leafletElement) {
      this.renderPopupContent()
      if (this.props.onOpen) {
        this.props.onOpen()
      }

      //sami
      if (this.props.isFocused) {
        const el = this.leafletElement
        setTimeout(() => {
          console.log('inside onPopupOpen', el)
          el._contentNode.firstElementChild.firstElementChild.focus()
        }, 5)
      }
    }
  }

  onPopupClose = ({ popup }: { popup: LeafletElement }) => {
    if (popup === this.leafletElement) {
      this.removePopupContent()
      if (this.props.onClose) {
        this.props.onClose()
      }
    }
  }

  renderPopupContent = () => {
    if (this.props.children == null) {
      this.removePopupContent()
    } else {
      render(
        Children.only(this.props.children),
        this.leafletElement._contentNode,
      )
      this.leafletElement.update()
      if (this.props.autoPan !== false) {
        if (this.leafletElement._map && this.leafletElement._map._panAnim) {
          this.leafletElement._map._panAnim = undefined
        }
        this.leafletElement._adjustPan()
      }
    }
  }

  removePopupContent = () => {
    if (this.leafletElement._contentNode) {
      unmountComponentAtNode(this.leafletElement._contentNode)
    }
  }

  render() {
    return null
  }
}
